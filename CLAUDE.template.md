# CLAUDE.md — Reglas operativas del proyecto **{{PROJECT_NAME}}**

> Leído automáticamente por Claude Code al iniciar sesión en este repo. Contiene las **reglas inviolables** y el **workflow**. Cualquier asistente IA o desarrollador humano opera dentro de estas reglas.
>
> Instanciado desde `framework_sdd/CLAUDE.template.md`. Última actualización: **AAAA-MM-DD** · Owner: **{{OWNER}}**

---

## ⚠️ Principio rector — CALIDAD sobre cantidad

**No buscamos iteraciones rápidas. Buscamos soluciones robustas y escalables a la primera.**

1. **Antes de codear, leer**: PRD canónico, spec técnica, estado del arte y el discovery del feature en curso.
2. **Antes de empezar, entender a fondo**: si el AC es ambiguo, clarificar — no asumir.
3. **Antes de cerrar, verificar**: test E2E real que cubre el AC + validar el JTBD + accesibilidad.
4. **Nunca dejar `TODO`/`FIXME`** en código commiteado.
5. **Nunca silenciar errores**: ni `try/catch` vacíos, ni `@ts-ignore`, ni `eslint-disable` sin razón documentada.
6. **Refactor cuando se entienda mejor**: no parchear sobre parche.
7. **Trazabilidad obligatoria**: commit → AC → UC → US → JTBD → ICP.

> Métrica de calidad: cada feature responde a *«¿por qué existe?»* (JTBD) y *«¿qué pasa si lo rompo?»* (tests E2E).

---

## 📚 Documentación — fuente de verdad

- Repo (`doc/`) = fuente de verdad del producto. {{KNOWLEDGE_TOOL}} y {{VAULT_TOOL}} = espejos navegables, siempre al día (por HU y por tarea).
- Cambio de mercado/ICP/JTBD → `doc/app/app_market.md`. Cambio de arquitectura/stack → `doc/app/app_spec.md` en la misma PR. Decisión nueva → ADR en `doc/decisions/ADR-NNN-<slug>.md`.
- `MEMORY.md` (raíz) = estado vivo de sesión, actualizado al cerrar.

Estructura: ver `framework_sdd/02-documentacion.md`.

---

## 🌿 Git workflow

```
main (PROTEGIDA) ← releases autorizadas, NUNCA merge directo
  └── develop ← rama de integración (staging despliega desde aquí)
        ├── feature/<slug> · feature/us-NN-uc-NNN-<slug>
        ├── fix/<slug> · chore/<slug> · spike/<topic>
```

- `main` protegida (PR + review + historia lineal + sin bypass). Merge a `main` sólo con autorización de **{{OWNER}}**.
- 1 feature = 1 rama. Squash merge a `develop`. No force-push a `develop`/`main`. `develop` siempre verde.
- Commits: `<type>(<scope>): <description> [refs]` — `feat|fix|refactor|test|docs|chore|perf|style|spike`.

Flujo de una tarea (validar feature branch en staging **antes** de mergear): ver `framework_sdd/03-git-workflow.md`.

---

## 🧪 Testing — E2E como única vía

- E2E (p. ej. Playwright) contra el staging real **{{DEV_URL}}**, no localhost.
- Cada AC funcional → ≥ 1 test E2E. DB/cache reales, RLS activas, stubs sólo si es legalmente necesario.
- Comando: `{{E2E_BASE_URL_ENV}}={{DEV_URL}} {{E2E_CMD}}`
- Gate de push: `{{VERIFY_CMD}}` verde. Gate de merge: deploy de la feature branch + E2E verde. Gate de cierre: PATCH back a `develop` + deploy + E2E verde.
- Coverage ≥ 85 % en core. Tests deterministas (flaky → causa raíz, no `skip`). Aislamiento por tenant (RLS).

Detalle y formato del test plan: ver `framework_sdd/04-testing.md`.

---

## 🚀 Despliegue — por API del PaaS · NO CI cloud

- Staging **{{DEV_URL}}** sirve `develop` por defecto y cualquier feature branch durante la validación (PATCH `git_branch` + deploy por API).
- **`main` → {{PROD_URL}} auto-deploy al mergear** (webhook). Merge a `main` requiere autorización de **{{OWNER}}**.
- Toda feature nueva detrás de **feature flag por grupo de tenants**, OFF por defecto en prod.
- Secretos en `.env.local` (gitignored) y en la UI del PaaS — nunca en el repo. Token/UUIDs: `{{DEPLOY_API_TOKEN_ENV}}`, `{{FRONTEND_DEV_UUID_ENV}}`, `{{BACKEND_DEV_UUID_ENV}}`.
- **PATCH back a `develop` antes de borrar una feature branch.** Nunca editar migraciones ya aplicadas.

Snippets y reglas: ver `framework_sdd/05-despliegue-y-versionado.md`.

---

## 🔢 Versionado visible

- Número de versión siempre visible en la UI (sidebar, abajo, gris suave). Fuente: `version.json` en la raíz.
- Dev `D{n} · {codename}` / Prod `{n} · {codename}`. Temática: {{VERSION_THEME}}.

---

## 🛠️ Pipeline SDD por feature

```
/discovery → /prd → /visual-setup → /plan → /implement → /acceptance-check → /quality-gate → /audit → /release
```

No saltar etapas. No marcar AC passed sin test E2E + revisión humana. No mergear sin quality-gate. Ver `framework_sdd/01-pipeline-sdd.md`.

---

## 🔒 Seguridad y RGPD

Residencia de datos acordada · secretos sólo en `.env.local` · credenciales cifradas (AES-256-GCM) · TLS 1.2+ · PII minimizada antes de modelos · log de auditoría inmutable por tenant · DPA antes de producción. Ver `framework_sdd/06-seguridad-rgpd.md`.

---

## 📝 Reglas inviolables (TL;DR)

Ver `framework_sdd/07-reglas-inviolables.md`. Las 3 que más se rompen:

1. ⚠️ Nunca mergear a `develop` código que no haya pasado E2E vs live primero.
2. ⚠️ Merge a `main` sólo con autorización expresa de **{{OWNER}}**.
3. ⚠️ Toda feature nueva detrás de feature flag, OFF por defecto en prod.

---

> **Última regla, la más importante**: si estas reglas chocan con una solicitud puntual, **mencionar el conflicto al humano antes de proceder**. No hacer trampas.
