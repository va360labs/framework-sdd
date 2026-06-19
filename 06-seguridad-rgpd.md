# 06 · Seguridad y RGPD

> Extracto operativo. Adaptar la residencia de datos y el alcance de PII a la legislación aplicable al proyecto.

---

## Reglas inviolables

1. **Residencia de datos** en la jurisdicción acordada (p. ej. UE) — no negociable cuando aplique.
2. **Nunca commitear secretos.** Viven en `.env.local` (gitignored) o en un gestor de secretos. Si por error se filtra uno → **rotarlo inmediatamente**.
3. **Credenciales de terceros cifradas en reposo** (p. ej. AES-256-GCM).
4. **TLS 1.2+ obligatorio** en tránsito.
5. **PII no se envía a modelos/terceros sin minimización** (capa pre-inferencia que enmascara datos personales).
6. **Log de auditoría inmutable por tenant** para acciones sensibles.
7. **DPA con cada cliente** antes de producción.
8. **Aislamiento multi-tenant** garantizado por RLS (Row Level Security) y validado por tests.

---

## Manejo de secretos — checklist

- [ ] Ningún secreto en `CLAUDE.md`, `MEMORY.md`, espejos de conocimiento ni código.
- [ ] `.env.local` está en `.gitignore`.
- [ ] Secretos de producción seteados **sólo en el PaaS** (UI), nunca en el repo.
- [ ] Tokens con el menor alcance posible (scoped).
- [ ] Plan de rotación: si un secreto aparece en un chat, PR o log → rotar antes de cerrar la sesión.

---

## Datos personales en tests

- No enviar PII real a proveedores externos en los tests.
- Usar dominios reservados (RFC 2606/6761: `.example`, `.test`, `.invalid`) o un dominio de test propio para los datos sintéticos.
- El teardown de tests **sólo** borra tenants/usuarios cuyos emails son de dominio reservado. **Nunca** borrar por "no está en la keep-list" — los datos reales/piloto son intocables.
