# 03 · Git workflow — branching y protección

---

## Estructura de ramas (inviolable)

```
main                  ← PROTEGIDA · sólo releases autorizadas · NUNCA merge directo
  └── develop         ← rama de integración · contra ella despliega el staging
        ├── feature/<feature-slug>      ← una rama por feature/HU
        ├── feature/us-NN-uc-NNN-<slug> ← si se trocea por UC
        ├── fix/<bug-slug>              ← hotfixes
        ├── chore/<task-slug>           ← tareas no-feature (docs, infra)
        └── spike/<topic>               ← spikes técnicos (descartables)
```

---

## Reglas inviolables

1. **`main` está PROTEGIDA.** Ningún merge directo. `main` recibe **sólo releases autorizadas** por el `{{OWNER}}`.
2. **Branch protection en el host Git** (configurar una vez):
   - `main`: PR obligatoria · ≥ 1 review · conversaciones resueltas · historia lineal · sin bypass · push restringido al owner.
   - `develop`: PR con review (sin restricción de push).
3. **Cada feature/HU/tarea = una rama.** Nunca dos features en la misma rama.
4. **Una PR por rama**, enlazada al AC/UC/US que cubre.
5. **Squash merge a `develop`.** Sin merge commits ruidosos.
6. **No force-push** a `develop` ni `main`. Sólo en ramas `feature/*` propias antes del PR.
7. **`develop` siempre verde.** Si lo rompes, lo arreglas antes de seguir.

---

## Convención de mensajes de commit

Formato: `<type>(<scope>): <description> [refs]`

Tipos: `feat` · `fix` · `refactor` · `test` · `docs` · `chore` · `perf` · `style` · `spike`.

```
feat(US-01/UC-001): tenant signup wizard en 4 pasos [AC-001..003]
test(US-04/UC-016): playwright cubre detección de idioma [AC-031]
fix(US-03/UC-012): idempotencia bajo reinyección 10x [AC-025]
docs(spec): registra decisión D-04 (protocolo email) [ADR-001]
chore(infra): script de deploy por API
```

---

## Flujo de una tarea (paso a paso)

> **Principio clave**: el entorno de staging ({{DEV_URL}}) puede servir **cualquier rama**, no sólo `develop`. Esto permite validar la feature branch contra el live **antes** de mergear. El merge a `develop` se hace sólo cuando los E2E ya confirmaron verde la rama desplegada — así `develop` nunca recibe código sin validar.

```bash
# 1. Partir de develop al día
git checkout develop && git pull origin develop

# 2. Crear rama de feature
git checkout -b feature/us-01-uc-001-<slug>

# 3. Implementar respetando los AC (cubrir cada AC con un test E2E,
#    actualizar app_spec.md si cambia arquitectura, actualizar docs)

# 4. Verificación local (bloqueante antes de push)
{{VERIFY_CMD}}          # typecheck + lint + format:check

# 5. Commit atómico
git commit -am "feat(US-01/UC-001): … [AC-001..003]"

# 6. Push de la rama
git push -u origin feature/us-01-uc-001-<slug>

# 7. PR contra develop (NUNCA contra main)
gh pr create --base develop --title "…" --body-file pr-body.md

# 8-10. Desplegar la feature branch al staging y correr E2E vs live
#       (ver 05-despliegue-y-versionado.md). Bloqueante para el merge.

# 11. Si E2E verde → squash-merge a develop
gh pr merge <N> --squash --delete-branch

# 12. PATCH del staging de vuelta a develop + redeploy (OBLIGATORIO
#     antes de cerrar sesión; ver 05). 

# 13. E2E vs staging sirviendo develop (sanity post-merge).
#     Si rompe → hotfix en rama nueva, NO revertir el merge.
```

> Nota single-dev: servir una feature branch en el staging compartido es seguro mientras trabaja una sola persona. Con más colaboradores, **comunicar** antes de cambiar la rama que sirve el staging.

---

## Plantilla de PR

El cuerpo del PR debe enlazar el AC/UC/US e incluir un **test plan** separando gates pre-merge (todos `[x]` al abrir el PR para mergear) de gates post-merge (`[ ]` legítimos hasta después del merge). Ver formato en [04 · Testing](04-testing.md).
