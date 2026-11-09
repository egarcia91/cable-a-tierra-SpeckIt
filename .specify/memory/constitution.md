# Cable a Tierra Constitution

## Core Principles

### I. Test-First (NO NEGOCIABLE)

- Todo cambio de comportamiento MUST empezar por un test que lo describa, escrito antes que la
  implementación.
- El ciclo MUST ser rojo-verde-refactor: el test falla por el motivo esperado, se escribe el
  mínimo código que lo hace pasar y recién entonces se refactoriza con los tests en verde.
- Las Firestore Security Rules y la lógica de servidor son comportamiento: MUST seguir el mismo
  ciclo, con tests contra los emuladores de Firebase.
- Un test en rojo MUST NOT borrarse, saltearse ni debilitarse para llegar a verde. Solo puede
  cambiar si el requisito que describe cambió.

**Razón**: las estadísticas y los permisos son el valor central del producto, y un test escrito
después tiende a confirmar lo que el código ya hace en lugar de lo que debería hacer.

### II. IA Aislada en un Módulo Dedicado

- Toda llamada a un modelo de IA MUST vivir en un único módulo dedicado. Ese módulo concentra
  los SDK de proveedores, los prompts y el parseo de las respuestas.
- La lógica de negocio MUST NOT importar SDK de proveedores, construir prompts ni llamar a
  modelos directamente; MUST consumir el módulo a través de una interfaz propia.
- La lógica de negocio MUST poder testearse sin invocar un modelo real.
- El MVP no usa IA. El módulo MUST NOT crearse hasta que una funcionalidad aprobada lo requiera.

**Razón**: aislar la IA permite cambiar de proveedor o de prompt sin tocar las reglas de negocio
y mantiene esas reglas deterministas y testeables.

### III. Nunca Inventar Datos

- El sistema MUST NOT mostrar ni persistir datos que no provengan de su fuente de verdad:
  Firestore y lo que cargan los usuarios autorizados.
- Un dato ausente MUST permanecer ausente. MUST NOT completarse con valores estimados, supuestos
  o generados.
- Las estadísticas MUST derivarse únicamente de partidos finalizados o corregidos, según RF-22.
- Ante una duda, el sistema MUST derivar a revisión humana en lugar de adivinar: rechaza la
  operación con un motivo claro o la deja a decisión del administrador/a de la organización.
- Toda salida de un modelo de IA MUST tratarse como propuesta no confirmada. Solo puede
  persistirse después de la validación de una persona autorizada, y MUST validarse contra la
  fuente de verdad.

**Razón**: un anotador solo sirve si sus números son confiables; un dato inventado es peor que
un dato faltante porque nadie sabe que hay que corregirlo.

### IV. Sin Secretos en el Código

- Las credenciales, claves de API, cuentas de servicio y tokens MUST NOT estar hardcodeados en
  el código, los tests, la configuración versionada, los logs ni la documentación.
- Los secretos MUST inyectarse por variables de entorno: `.env.local` en desarrollo, que MUST
  estar excluido de Git, y la configuración de entorno de Vercel en producción.
- `.env.example` MUST listar las variables necesarias solo con valores de ejemplo.
- Los secretos MUST NOT llegar al cliente: las variables `NEXT_PUBLIC_` quedan reservadas para
  valores que son públicos por diseño, como la configuración web de Firebase.
- Un secreto que llegue a commitearse MUST considerarse comprometido y rotarse; borrarlo del
  código no alcanza.

**Razón**: el historial de Git es permanente y un secreto filtrado da acceso directo a los datos
de los jugadores/as.

## Restricciones del Proyecto

- El stack es el definido en `AGENTS.md`: Next.js 15 con App Router y TypeScript, Node 22 LTS,
  Firestore, Firebase Auth solo con Google, Vitest, Playwright y Vercel.
- Los requisitos del producto y su alcance viven en `PRD.md`. Las restricciones de la sección
  "Qué NO hacer" de `AGENTS.md` son obligatorias.
- Los permisos MUST validarse en Firestore Security Rules o en el servidor, nunca solo en la UI
  (RNF-01).

## Flujo de Desarrollo y Puertas de Calidad

- Cada plan de implementación MUST verificar el cumplimiento de los cuatro principios antes de
  empezar y justificar por escrito cualquier desvío.
- En cada lista de tareas, las tareas de test MUST preceder a las de implementación que cubren.
- Un cambio MUST NOT integrarse a `main` si falla `npm test`, `npm run test:integration` o
  `npm run test:e2e`, en la medida en que el cambio afecte a esa capa.
- Cada regla de permisos MUST tener un test que intente la operación no permitida directamente
  contra la base de datos.

## Governance

- Esta constitución prevalece sobre cualquier otra práctica del proyecto. Si otro documento la
  contradice, MUST corregirse ese documento o enmendarse la constitución.
- Toda enmienda MUST hacerse por un cambio versionado en este archivo, con su motivo y la fecha
  de la modificación.
- El versionado es semántico:
  - MAJOR: se elimina o se redefine un principio de forma incompatible.
  - MINOR: se agrega un principio o sección, o se amplía materialmente una guía.
  - PATCH: aclaraciones y correcciones de redacción sin cambio de significado.
- Toda revisión de código MUST verificar el cumplimiento de esta constitución.

**Version**: 1.0.0 | **Ratified**: 2026-11-09 | **Last Amended**: 2026-11-09
