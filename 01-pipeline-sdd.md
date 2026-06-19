# 01 · Pipeline SDD — orden de ejecución por feature

> Para cada feature nueva se sigue este pipeline **en orden**. No se salta ninguna etapa.

---

## Las etapas

```
1. /discovery <feature>        ← ICPs/JTBDs + drift + evidencia
2. /prd <feature>              ← PRD con USs/UCs/ACs tagged con JTBDs
3. /visual-setup (1 sola vez)  ← Brand tokens + diseños + variantes por ICP
4. /plan <feature>             ← Plan técnico: UCs → componentes → tests
5. /implement <feature>        ← Implementación + qualitative gate
6. /acceptance-check <feature> ← Validar AC end-to-end
7. /quality-gate               ← Lint, coverage, regresión
8. /audit (periódico)          ← Auditoría de calidad (ISO 25010)
9. /release (cuando aplique)   ← Cortar release con changelog
```

> Si no hay tooling de skills conectado, se ejecutan **manualmente replicando el flujo**: cada etapa produce su artefacto en `doc/`.

---

## Jerarquía de especificación

```
ICP            ← Perfil de cliente ideal (a quién servimos)
 └── JTBD      ← Job To Be Done (qué problema resolvemos y por qué)
      └── US   ← User Story (como <rol> quiero <algo> para <beneficio>)
           └── UC   ← Use Case (flujo concreto)
                └── AC   ← Acceptance Criterion (condición verificable)
```

Cada **AC** lleva un ID estable (`AC-NNN`). Cada AC funcional tiene **≥ 1 test E2E** que lo verifica.

---

## Cadena de trazabilidad (obligatoria)

```
commit  →  AC  →  UC  →  US  →  JTBD  →  ICP
```

- El **mensaje de commit** referencia el/los AC: `feat(US-01/UC-001): … [AC-001..003]`.
- El **nombre del test** contiene el AC ID: `test('AC-001: …')`.
- Trazabilidad inversa: `grep AC-NNN` en el repo encuentra el test, el AC en el `prd.md`, su UC/US, su JTBD y su ICP.

Si un cambio no se puede trazar hasta un AC, **no se mergea**.

---

## Reglas inviolables del pipeline

1. **No saltar etapas.** No `/plan` sin `/prd`. No `/implement` sin `/plan`.
2. **No marcar un AC como passed** sin: test E2E + qualitative gate (si es un AC de juicio cualitativo) + revisión humana.
3. **No mergear sin `quality-gate` pasado.**
4. **Cada feature nueva detrás de feature flag** (ver [05](05-despliegue-y-versionado.md)).

---

## Artefactos por etapa

| Etapa             | Artefacto en repo                                          |
| ----------------- | ---------------------------------------------------------- |
| discovery         | `doc/discovery/<feature>/icp_jtbd.md`                      |
| prd               | `doc/discovery/<feature>/prd.md` (USs/UCs/ACs)             |
| visual-setup      | `tokens/` + diseños                                        |
| plan              | `doc/plans/<feature>_plan.md`                              |
| implement         | código + tests E2E                                         |
| acceptance-check  | reporte de AC verdes                                       |
| quality-gate      | reporte lint/coverage/regresión                            |
| release           | changelog + tag de versión                                 |
