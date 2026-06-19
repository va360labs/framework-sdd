# 00 · Principio rector — Calidad sobre cantidad

> **No buscamos iteraciones rápidas. Buscamos soluciones robustas y escalables a la primera.**

---

## Las 7 reglas operativas

1. **Antes de escribir código, leer.** PRD canónico, spec técnica, estado del arte y el discovery del feature en curso. No se codea a ciegas.
2. **Antes de empezar una tarea, entenderla a fondo.** Si el AC tiene ambigüedad → **clarificar primero**, no asumir.
3. **Antes de cerrar una tarea, verificar.** Cubrir el AC con un test E2E real, validar el JTBD asociado, revisar accesibilidad.
4. **Nunca dejar `TODO`/`FIXME` en código commiteado.** Si algo no está terminado, no se commitea.
5. **Nunca silenciar errores.** Si algo falla, se entiende y se arregla. Prohibido:
   - `try/catch` vacíos.
   - `// @ts-ignore` / `# type: ignore` sin razón documentada.
   - `eslint-disable` / supresiones de linter sin razón documentada.
6. **Refactor cuando se entienda mejor.** No parchear sobre parche.
7. **Trazabilidad obligatoria.** Cada commit → tarea → AC → UC → US → JTBD → ICP. Si no se puede trazar, no se mergea.

---

## Métrica de calidad

Cada feature debe poder responder por sí sola a dos preguntas:

- **¿Por qué existe?** → su JTBD (Job To Be Done).
- **¿Qué pasa si lo rompo?** → los tests E2E que cubren sus AC.

Si una de las dos respuestas no existe, la feature no está terminada.

---

## Antipatrones prohibidos

| Antipatrón                                   | Por qué está prohibido                                          |
| -------------------------------------------- | -------------------------------------------------------------- |
| Parche rápido "para salir del paso"          | Genera deuda y rompe la regla de robustez a la primera.        |
| Degradar datos visibles con placeholders     | Si la vista no puede recalcular un dato, congela el último valor conocido — nunca lo ocultes ni muestres `·`/`—`. |
| Añadir funcionalidad no pedida               | Reporta el bug o pregunta. No metas controles/features que nadie pidió. |
| Editar artefactos inmutables ya aplicados    | P. ej. migraciones ya desplegadas (rompe checksum del runner). Ver [05](05-despliegue-y-versionado.md). |
| Marcar un AC como passed sin test            | Rompe la trazabilidad verde verificable.                       |
