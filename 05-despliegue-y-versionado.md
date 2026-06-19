# 05 · Despliegue y versionado

> Donde el proyecto tenga un documento imperativo propio de despliegue, **ese documento manda** sobre este.

---

## Modelo de entornos

| Entorno     | Rama servida           | URL              | Despliegue                                         |
| ----------- | ---------------------- | ---------------- | -------------------------------------------------- |
| **Staging** | `develop` (o cualquier feature branch durante la validación) | {{DEV_URL}}  | Por API del PaaS (PATCH `git_branch` + deploy)     |
| **Prod**    | `main`                 | {{PROD_URL}}     | **Automático al mergear a `main`** (webhook)        |

---

## Reglas de despliegue inviolables

1. **`main` está PROTEGIDA.** Merge a `main` = **release autorizada por el `{{OWNER}}`**. Nada de merge directo. (Se recomienda un hook que bloquee `push :main` / `pr merge` base `main` salvo marcador explícito de release.)
2. **Merge autorizado a `main` ⇒ auto-deploy a producción** vía webhook del PaaS (no un CI cloud).
3. **Staging es para validación, no producción.** Sirve `develop` por defecto y cualquier feature branch durante la validación pre-merge.
4. **Nunca desplegar a prod** sin antes haber pasado el sanity E2E en staging sirviendo `develop`.
5. **PATCH de vuelta a `develop`** es obligatorio antes de cerrar sesión si durante la sesión se sirvió una feature branch.
6. **No borrar una feature branch** sin antes hacer PATCH back a `develop` — el PaaS intentará el siguiente deploy contra una rama inexistente y fallará al clonar.
7. **Variables de entorno y secretos se gestionan en el PaaS** (UI/API), no se commitean. Ojo: muchos PaaS **no persisten secretos vía API** → setear secretos por UI.
8. **No usar CI cloud (GitHub Actions y similares)** para test/deploy. El runner es el dev atacando el staging. Excepciones (alertas de dependencias, lint si el equipo crece) sólo con ADR.

---

## Feature flags — obligatorio

- **Toda funcionalidad nueva va detrás de un feature flag por grupo de tenants.**
- En producción se despliega **OFF por defecto** para todos los grupos.
- El `{{OWNER}}` activa por grupo quién tiene acceso.
- La plantilla de PR debe exigir el flag.

---

## Versionado visible

1. **El número de versión se ve siempre** en la UI (sidebar abajo, pequeño, gris suave).
2. **Fuente de verdad** = `version.json` en la raíz del repo. Debe ser **real y barato de actualizar** (no requerir cambios grandes).
3. Esquema de nombres: `{{VERSION_THEME}}`.
   - **Dev**: `D{n} · {codename}`
   - **Prod**: `{n} · {codename}`
4. Datos de prueba en dev se **purgan** tras las pruebas (cubierto por el teardown E2E).

---

## Snippet de deploy por API (genérico)

```bash
# 0. Inspeccionar estado actual (qué rama sirve + health)
curl -H "Authorization: Bearer ${{DEPLOY_API_TOKEN_ENV}}" \
  "{{DEPLOY_API_BASE_URL}}/api/v1/applications/<app-uuid>"

# 1. Cambiar la rama que sirve
curl -X PATCH -H "Authorization: Bearer ${{DEPLOY_API_TOKEN_ENV}}" \
  -H "Content-Type: application/json" \
  "{{DEPLOY_API_BASE_URL}}/api/v1/applications/<app-uuid>" \
  -d '{"git_branch":"feature/us-XX-uc-YYY-slug"}'

# 2. Desplegar (re-pull + rebuild + restart)
curl -X POST -H "Authorization: Bearer ${{DEPLOY_API_TOKEN_ENV}}" \
  "{{DEPLOY_API_BASE_URL}}/api/v1/deploy?uuid=<app-uuid>"

# 3. Poll del deploy hasta `finished`
curl -H "Authorization: Bearer ${{DEPLOY_API_TOKEN_ENV}}" \
  "{{DEPLOY_API_BASE_URL}}/api/v1/deployments/<deployment-uuid>"
```

> UUIDs y token canónicos en `.env.local` (gitignored): `{{DEPLOY_API_TOKEN_ENV}}`, `{{FRONTEND_DEV_UUID_ENV}}`, `{{BACKEND_DEV_UUID_ENV}}`.

**Scope del deploy**: si la PR sólo toca frontend, sólo PATCH+deploy del frontend. Si toca backend (migraciones, módulos, schema), también el backend.

---

## Artefactos inmutables

- **Nunca editar migraciones ya aplicadas** — el runner valida checksum y un cambio (aunque sea un comentario) rompe el deploy. Todo sweep de rename/refactor masivo debe **excluir** la carpeta de migraciones aplicadas.
- Cada deploy queda registrado y referenciado en el changelog.
- Rollback documentado en un runbook.
