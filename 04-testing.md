# 04 · Testing — E2E como única vía

> El testing E2E se ejecuta contra el **entorno de staging real** ({{DEV_URL}}), no contra `localhost`. Así los tests golpean siempre la versión más reciente con datos reales, RLS real y TLS real.

---

## Por qué staging y no localhost

- El entorno local no está siempre arriba; exigirlo desincentiva el testing.
- El staging ya tiene base de datos real, certificado TLS real, políticas de seguridad cargadas y los conectores configurados → los tests cubren el camino completo (incluido el chain de deploy).
- El PaaS de deploy acepta servir cualquier feature branch en minutos → el flujo es: desplegar la rama → E2E → mergear → desplegar `develop` → E2E (sanity).

Comando canónico:

```bash
{{E2E_BASE_URL_ENV}}={{DEV_URL}} {{E2E_CMD}}
```

---

## Gates: cuándo es bloqueante

| Momento                         | Verificación bloqueante                                                                | Motivo                                                        |
| ------------------------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------ |
| **Antes de `git push`**         | `{{VERIFY_CMD}}` verde (typecheck + lint + format).                                     | La rama compila, tiene estilo coherente y tipos cuadran.     |
| **Antes de mergear la PR**      | Staging desplegado con la **feature branch** + E2E vs {{DEV_URL}} **verde**.           | Validar el código real contra el live antes de meterlo a `develop`. |
| **Post-merge, antes de cerrar** | Staging de vuelta a `develop` + deploy + E2E vs {{DEV_URL}} **verde**.                  | Confirmar que `develop` desplegado sigue verde.              |

> **Anti-regla**: nunca mergear esperando que el deploy post-merge sea el único gate. El gate de merge es E2E vs live **sirviendo la feature branch**.

---

## Reglas inviolables

1. **E2E es la herramienta única.** No se introduce otra sin justificar (ADR).
2. **Cada AC funcional → ≥ 1 test E2E** que lo verifica end-to-end, lo más cercano a producción posible.
3. **Tests "lo más reales posible"**: DB real (RLS activas), cache real, modelos reales o stubs declarativos **sólo** cuando es legalmente necesario (no enviar PII a un proveedor real en tests).
4. **`{{VERIFY_CMD}}` antes de push** (bloqueante). **E2E vs staging** (bloqueante para cerrar sesión).
5. **Coverage objetivo ≥ 85 %** en módulos del core.
6. **Tests deterministas**: si un test es flaky, se arregla la causa raíz, **no** se marca `skip`.
7. **Datos de prueba aislados por tenant** (RLS validado). Cada test crea su propio tenant con slug único — sin estado compartido.
8. **Cleanup de datos de prueba** tras las pruebas (teardown), preservando datos reales/piloto.

---

## Mapeo AC → test

El nombre del test **debe contener el AC ID**:

```ts
test('AC-001: Owner se registra y accede al tenant aislado en <60s', async ({ page }) => {
  const start = Date.now();
  await page.goto('/signup');
  // …
  expect(Date.now() - start).toBeLessThan(60_000);
});
```

Estructura recomendada:

```
tests/
├── e2e/
│   ├── us-01-<slug>/
│   │   ├── uc-001-<slug>.spec.ts
│   │   └── uc-002-<slug>.spec.ts
│   └── us-02-<slug>/
├── fixtures/
└── playwright.config.ts   ← baseURL leída de {{E2E_BASE_URL_ENV}} (default {{DEV_URL}})
```

---

## Trazabilidad inversa

```
test "AC-NNN" → grep en repo → AC en doc/discovery/<feature>/prd.md
             → UC y US asociados → JTBD → ICP en doc/app/app_market.md
```

---

## Formato del test plan en la PR

```markdown
## Test plan

### Pre-merge gates (bloqueantes — todos marcados al abrir el PR para merge)
- [x] `{{VERIFY_CMD}}` verde local
- [x] Staging desplegado con esta rama + deploy finished
- [x] E2E vs {{DEV_URL}} (sirviendo esta rama) → AC-XXX + AC-YYY verdes
- [x] Regresión completa: N/N verde

### Post-merge gates (bloqueantes para cerrar sesión)
- [ ] Staging de vuelta a `develop` + deploy finished
- [ ] E2E vs {{DEV_URL}} (sirviendo develop) verde
- [ ] MEMORY.md actualizado
```

Los `[ ]` post-merge son legítimos al abrir el PR (no pueden cumplirse hasta después del merge).
