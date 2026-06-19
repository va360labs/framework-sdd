<div align="center">

<table><tr><td bgcolor="#0d0d0d" align="center" style="padding: 32px 48px; border-radius: 12px;">
  <img src="https://va360labs.com/public/imagen/logo-va360.webp" width="280" alt="VA360 Labs" />
</td></tr></table>

<br/>

# SDD Framework · Spec-Driven Development

**El framework de ingeniería con el que VA360 Labs construye software que funciona — a la primera.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)
[![Made by VA360 Labs](https://img.shields.io/badge/Made%20by-VA360%20Labs-gold?style=for-the-badge)](https://va360labs.com)
[![Learn at VA360 Academy](https://img.shields.io/badge/Learn%20at-VA360%20Academy-blueviolet?style=for-the-badge)](https://va360.academy)

[va360labs.com](https://va360labs.com) · [va360.academy](https://va360.academy)

</div>

---

## ¿Qué es SDD?

> **Spec-Driven Development** = la especificación manda sobre el código. Nada se implementa sin que exista antes su razón (JTBD), su criterio de aceptación (AC) y su test que lo verifica end-to-end.

El SDD Framework es el método operativo que usamos en VA360 Labs en todos nuestros proyectos. No es teoría — es el resultado destilado de años construyendo productos reales con equipos reales. Cada línea de código en un proyecto SDD se puede trazar hacia atrás hasta el problema del usuario que resuelve.

**Tres pilares:**

| Pilar | Qué significa |
|---|---|
| **Calidad sobre cantidad** | Soluciones robustas a la primera. Sin parches, sin deuda técnica acumulada. |
| **Trazabilidad total** | `commit → AC → UC → US → JTBD → ICP`. Si no se puede trazar, no se mergea. |
| **Verde verificable** | Cada criterio de aceptación tiene un test E2E real contra el entorno más cercano a producción posible. |

---

## El pipeline — 9 etapas sin atajos

```
1. /discovery   ←  ICPs + JTBDs + evidencia de mercado
2. /prd         ←  Product Requirements con USs / UCs / ACs vinculados a JTBDs
3. /visual-setup←  Brand tokens + diseños + variantes por ICP (1 sola vez)
4. /plan        ←  Plan técnico: UCs → componentes → tests
5. /implement   ←  Código + quality gate cualitativo
6. /acceptance-check ← Validar ACs end-to-end
7. /quality-gate←  Lint, coverage, regresión
8. /audit       ←  Auditoría de calidad ISO 25010 (periódica)
9. /release     ←  Changelog + tag de versión
```

**No se salta ninguna etapa.** No existe `/plan` sin `/prd`. No existe `/implement` sin `/plan`. La disciplina es el producto.

---

## La cadena de trazabilidad

```
ICP  ──►  JTBD  ──►  US  ──►  UC  ──►  AC  ──►  test E2E  ──►  commit
```

Cada **AC** lleva un ID estable (`AC-NNN`). Cada AC funcional tiene ≥ 1 test E2E. Un `grep AC-042` en cualquier repo SDD encuentra el test, el AC en el PRD, su UC, su US, su JTBD y su ICP. En segundos.

---

## Contenido del framework

| # | Documento | Qué cubre |
|---|---|---|
| 00 | [Principio rector](00-principio-rector.md) | Las 7 reglas operativas + antipatrones prohibidos |
| 01 | [Pipeline SDD](01-pipeline-sdd.md) | Etapas, jerarquía de spec, artefactos por etapa |
| 02 | [Documentación](02-documentacion.md) | Fuente de verdad, estructura `doc/`, ADRs |
| 03 | [Git workflow](03-git-workflow.md) | Branching, protección de `main`, commits convencionales |
| 04 | [Testing](04-testing.md) | E2E como única vía, gates de push/merge/cierre |
| 05 | [Despliegue y versionado](05-despliegue-y-versionado.md) | Deploy por API, feature flags, versión visible |
| 06 | [Seguridad y RGPD](06-seguridad-rgpd.md) | Secretos, cifrado, residencia de datos, auditoría |
| 07 | [Reglas inviolables](07-reglas-inviolables.md) | El resumen de una página que nunca se rompe |
| — | [CLAUDE.template.md](CLAUDE.template.md) | Plantilla lista para instanciar en un proyecto nuevo |

---

## Adoptar el framework en 6 pasos

```bash
# 1. Clona el framework en tu proyecto
git clone https://github.com/va360labs/framework-sdd framework_sdd

# 2. Instancia la plantilla
cp framework_sdd/CLAUDE.template.md CLAUDE.md

# 3. Rellena los placeholders en CLAUDE.md
#    {{PROJECT_NAME}}, {{OWNER}}, {{DEV_URL}}, {{PROD_URL}}, ...

# 4. Configura branch protection en main y develop
#    (ver 03-git-workflow.md)

# 5. Crea version.json y muéstralo en la UI
#    (ver 05-despliegue-y-versionado.md)

# 6. Arranca con la primera feature
#    /discovery <feature>
```

---

## Las 15 reglas inviolables

> Si alguna de estas reglas choca con una petición puntual → **avisar al humano antes de proceder**. No hacer trampas.

1. ⚠️ Calidad > cantidad. Soluciones robustas, no parches rápidos.
2. ⚠️ `main` protegida. `develop` es la rama de deploy. Una feature = una rama.
3. ⚠️ Gate de push: verify verde. Gate de merge: E2E contra staging verde. Gate de cierre: PATCH a `develop` + E2E verde.
4. ⚠️ No CI cloud. Deploy vía API del PaaS desde local.
5. ⚠️ Docs sincronizadas siempre. El repo es la única fuente de verdad.
6. ⚠️ Trazabilidad obligatoria: cada AC → test → commit → JTBD → ICP.
7. ⚠️ Nunca silenciar errores ni dejar `TODO`/`FIXME` en código commiteado.
8. ⚠️ Secretos sólo en `.env.local` (gitignored). Si se filtra, rotar.
9. ⚠️ Pipeline SDD completo sin atajos.
10. ⚠️ Toda feature nueva detrás de feature flag, OFF por defecto en prod.
11. ⚠️ Versión visible siempre en la UI. `version.json` real y actualizado.
12. ⚠️ Nunca editar migraciones ya aplicadas (rompe checksum del runner).
13. ⚠️ No degradar datos visibles con placeholders — congelar el último valor conocido.
14. ⚠️ No añadir nada no pedido. Reportar un bug ≠ pedir un control nuevo.
15. ⚠️ `MEMORY.md` actualizado al final de cada sesión.

---

## Por qué funciona

La mayoría de los proyectos fracasan en el gap entre la intención y la implementación. El equipo entiende una cosa, el código hace otra, los tests verifican algo diferente y el producto llega a producción roto.

SDD elimina ese gap por construcción:

- **Antes de codear, existe una spec.** No hay ambigüedad sobre qué construir.
- **Antes de mergear, existe un test verde.** No hay fe. Hay evidencia.
- **Después del deploy, existe trazabilidad.** No hay "¿quién tocó esto?". Hay un `grep`.

El resultado: software que funciona, equipos que no apagan incendios y productos que escalan.

---

<div align="center">

## Aprende a construir software así

Este framework nació en [VA360 Labs](https://va360labs.com), el estudio de ingeniería de producto que ayuda a equipos a construir software que funciona — y que escala.

Si quieres aprender a aplicar SDD, los principios de ingeniería detrás de este framework y cómo construir productos digitales con este nivel de rigor, visita **[va360.academy](https://va360.academy)**.

<br/>

**[→ va360labs.com](https://va360labs.com)** — Estudio de ingeniería de producto

**[→ va360.academy](https://va360.academy)** — Aprende a construir software que funciona

<br/>

[![Instagram](https://img.shields.io/badge/@valentin__ayesa-E4405F?style=for-the-badge&logo=instagram&logoColor=white)](https://www.instagram.com/valentin_ayesa/)
[![LinkedIn](https://img.shields.io/badge/valentinayesa-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/valentinayesa/)
[![YouTube](https://img.shields.io/badge/VA360_Labs-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/channel/UCxNmXU0TEFM7DqJahdLKNCQ)
[![TikTok](https://img.shields.io/badge/@va360__valen-000000?style=for-the-badge&logo=tiktok&logoColor=white)](https://www.tiktok.com/@va360_valen)

<br/>

<table><tr><td bgcolor="#0d0d0d" align="center" style="padding: 24px 48px; border-radius: 8px;">
  <img src="https://va360labs.com/public/imagen/logo-va360.webp" width="180" alt="VA360 Labs" />
  <br/><br/>
  <sub style="color: #888;">Built with discipline. Shipped with evidence.</sub>
</td></tr></table>

</div>

---

## Licencia

MIT — úsalo, adáptalo, mejóralo. Si construyes algo con esto, cuéntanos en [va360labs.com](https://va360labs.com).
