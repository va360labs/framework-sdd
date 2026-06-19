# 02 · Documentación — fuente de verdad

> La documentación no es opcional ni "se actualiza luego". Se mantiene **siempre al día, por User Story y por tarea**.

---

## Reglas inviolables

1. **El repositorio (`doc/`) es la fuente de verdad del producto.** Cualquier espejo externo ({{KNOWLEDGE_TOOL}}, {{VAULT_TOOL}}) es navegable, no canónico.
2. **Se actualiza por HU y por tarea.** Al completar un AC, se actualiza su estado donde corresponda.
3. **Cambio de mercado/ICP/JTBD** → `doc/app/app_market.md` + validar drift.
4. **Cambio de arquitectura/stack/entidades** → `doc/app/app_spec.md` **en la misma PR**.
5. **Decisión nueva o reabierta** → un **ADR** en `doc/.../ADR-NNN-<slug>.md`.

---

## Estructura canónica de `doc/`

```
doc/
├── app/
│   ├── app_market.md      ← Canon de mercado (ICPs, JTBDs)
│   ├── app_prd.md         ← PRD de producto
│   └── app_spec.md        ← Spec técnica (stack, arquitectura, entidades)
├── discovery/
│   └── <feature>/
│       ├── icp_jtbd.md    ← Veredicto del discovery
│       └── prd.md         ← PRD del feature (USs/UCs/ACs)
├── plans/
│   └── <feature>_plan.md  ← Plan técnico
└── decisions/             ← ADRs (Architecture Decision Records)
    └── ADR-NNN-<slug>.md
```

---

## ADRs — Architecture Decision Records

Toda decisión técnica relevante (o reabierta) se registra en un ADR numerado e inmutable. Estructura mínima:

```markdown
# ADR-NNN — <título de la decisión>

- **Estado**: propuesta | aceptada | reemplazada por ADR-MMM
- **Fecha**: AAAA-MM-DD
- **Contexto**: qué problema obliga a decidir.
- **Decisión**: qué se decide.
- **Consecuencias**: qué gana y qué cuesta. Qué riesgos abre.
- **Alternativas descartadas**: y por qué.
```

Un ADR no se edita una vez aceptado; si la decisión cambia, se crea un ADR nuevo que **reemplaza** al anterior.

---

## Cuándo actualizar qué

| Cambio                  | Repo                            | Espejo navegable                  |
| ----------------------- | ------------------------------- | --------------------------------- |
| Nuevo AC implementado   | Test E2E + commit               | Checkbox del AC en el PRD         |
| Nueva US/UC             | `prd.md`                        | Nota de la US                     |
| Decisión cerrada        | `ADR-NNN`                       | Página de riesgos y decisiones    |
| Riesgo nuevo            | `app_market.md` / `prd.md`      | Nota de riesgos                   |
| Cambio arquitectónico   | `app_spec.md`                   | Nota de arquitectura              |
| Cambio de mercado/ICP   | `app_market.md` + revisar drift | Market canvas                     |

---

## Estado vivo de sesión (`MEMORY.md`)

- En la raíz del repo, junto al `CLAUDE.md`.
- Se actualiza **al final de cada sesión** para poder retomar desde otro dispositivo o por otra persona.
- Una línea por hito; el detalle va en ficheros de tema enlazados. Mantener el índice corto.
- **Nunca** contiene secretos.
