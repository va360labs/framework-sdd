# 07 · Reglas inviolables (TL;DR)

> El resumen de una página. Si algo aquí choca con una petición puntual → **avisar al humano antes de proceder**. No hacer trampas.

1. ⚠️ **Calidad > cantidad.** Soluciones robustas, no parches rápidos.
2. ⚠️ **`main` protegida.** `develop` es la rama de deploy. Una feature = una rama. Merge a `main` sólo con autorización del `{{OWNER}}`.
3. ⚠️ **Gates:**
   - *Push*: `{{VERIFY_CMD}}` verde.
   - *Merge*: staging desplegado con la **feature branch** + E2E vs {{DEV_URL}} verde.
   - *Cierre de sesión*: PATCH back a `develop` + deploy + E2E vs live verde.
   - **Nunca mergear a `develop` código que no haya pasado E2E vs live primero.**
4. ⚠️ **No CI cloud.** Deploy vía API del PaaS desde local.
5. ⚠️ **Docs sincronizadas siempre** (repo = verdad; espejos navegables al día). Por HU y por tarea.
6. ⚠️ **Trazabilidad obligatoria:** cada AC → test → commit → JTBD → ICP.
7. ⚠️ **Nunca silenciar errores ni dejar `TODO`/`FIXME`** en código commiteado.
8. ⚠️ **Secretos sólo en `.env.local` (gitignored).** Si se filtra, rotar.
9. ⚠️ **Pipeline SDD completo:** discovery → prd → visual-setup → plan → implement → acceptance-check → quality-gate.
10. ⚠️ **Toda feature nueva detrás de feature flag**, OFF por defecto en prod.
11. ⚠️ **Versión visible siempre** en la UI; `version.json` real y actualizado.
12. ⚠️ **Nunca editar migraciones ya aplicadas** (rompe checksum del runner).
13. ⚠️ **No degradar datos visibles** con placeholders — congelar el último valor conocido.
14. ⚠️ **No añadir nada no pedido.** Reportar un bug ≠ pedir un control nuevo.
15. ⚠️ **`MEMORY.md` actualizado** al final de cada sesión.
