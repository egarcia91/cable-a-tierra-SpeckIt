# Feature Specification: Cable a Tierra — anotador de partidos de amigos (MVP solo fútbol)

**Feature Branch**: `main` (no se creó una rama de feature)

**Created**: 2026-11-10

**Status**: Draft

**Input**: User description: "Generá el spec a partir del PRD en docs/PRD.md". Fuente: `PRD.md`
(PRD-001), en la raíz del repositorio. Cada requisito cita su ID de origen en el PRD (RF, RNF o
AC) para mantener la trazabilidad.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Anotar un partido y acumular estadísticas (Priority: P1)

Una persona inicia sesión con Google, crea su organización y queda como administrador/a. Carga a
los jugadores/as del grupo, da de alta un partido con día, lugar y los dos equipos y, cuando se
juega, lo finaliza cargando los goles de cada jugador/a. Cada jugador/a de la organización suma
un partido, sus goles y una victoria, un empate o una derrota.

**Why this priority**: es el corazón del producto. Sin anotar partidos no hay estadísticas, y la
métrica principal del PRD mide partidos finalizados.

**Independent Test**: con una cuenta de Google, crear una organización con dos jugadores/as, dar
de alta un partido de uno contra uno y finalizarlo. Las estadísticas de ambos deben reflejar el
resultado.

**Acceptance Scenarios**:

1. **Dado** un usuario sin cuenta, **cuando** inicia sesión con Google por primera vez, **entonces**
   queda registrado y con la sesión iniciada (AC-09b).
2. **Dado** un usuario registrado, **cuando** crea una organización, **entonces** la organización
   queda creada y ese usuario queda como su administrador/a (AC-10a, AC-10b).
3. **Dado** un administrador/a, **cuando** crea un jugador/a con nombre "Juan Pérez", apodo
   "Juancho" y fecha de nacimiento 10 de mayo de 1990, **entonces** queda guardado con esos
   valores y con 0 partidos, 0 goles, 0 victorias, 0 empates y 0 derrotas (AC-04).
4. **Dado** un administrador/a, **cuando** da de alta un partido con día, lugar y jugadores/as
   por equipo, **entonces** el partido queda creado como pendiente (AC-14).
5. **Dado** un partido pendiente, **cuando** el administrador/a lo finaliza cargando 2 goles para
   un jugador/a del equipo A y 1 para uno del equipo B, **entonces** queda finalizado 2 a 1
   (AC-53).
6. **Dado** un jugador/a con 7 partidos y 4 goles que convierte 2 goles en un partido que su
   equipo gana 5 a 3, **cuando** se finaliza el partido, **entonces** queda con 8 partidos,
   6 goles y una victoria más (AC-01, AC-02, AC-03a). Para el empate y la derrota aplican AC-03b
   y AC-03c.
7. **Dado** un partido pendiente, **cuando** el administrador/a carga 2 goles para un jugador/a
   del equipo A y 1 gol en contra hecho por un jugador/a del equipo A, **entonces** el marcador
   queda 2 a 1 a favor del equipo A y el gol en contra no se acredita a nadie (AC-15).
8. **Dado** un partido con un jugador/a externo/a "Tano" del equipo A que convierte 2 goles y
   ningún otro gol, **cuando** se finaliza, **entonces** el marcador queda 2 a 0 a favor del
   equipo A, "Tano" aparece como "externo/a" y no registra estadísticas (AC-55, AC-23a, AC-23b,
   AC-23c).
9. **Dado** un partido finalizado, **cuando** el administrador/a intenta finalizarlo de nuevo,
   **entonces** el sistema lo rechaza y las estadísticas no cambian (AC-61).
10. **Dado** un partido pendiente cuyo día es mañana en hora de Argentina, **cuando** el
    administrador/a intenta finalizarlo, **entonces** el sistema lo rechaza. Si el día es hoy, lo
    acepta (AC-66, AC-67).

También aplican AC-09a, AC-11, AC-27, AC-36, AC-37, AC-54, AC-62 a AC-65 y AC-72.

---

### User Story 2 - Mirar organizaciones, partidos y estadísticas sin ver datos personales (Priority: P1)

Cualquier persona, sin iniciar sesión o con una cuenta que no es miembro de la organización, puede
recorrer el listado de organizaciones, ver los partidos de cada una y el listado de sus
jugadores/as con sus estadísticas. De cada jugador/a ve solo su apodo y sus estadísticas, nunca su
nombre completo ni su fecha de nacimiento.

**Why this priority**: es lo que hace visible el esfuerzo de los jugadores/as, y la privacidad de
sus datos es una restricción no negociable del proyecto.

**Independent Test**: con datos cargados, entrar en modo invitado y recorrer organizaciones,
partidos y jugadores/as. Verificar que nunca se recibe el nombre completo ni la fecha de
nacimiento, ni siquiera leyendo los datos sin pasar por la interfaz.

**Acceptance Scenarios**:

1. **Dado** un usuario sin sesión, **cuando** elige ingresar en modo invitado, **entonces** ve el
   listado de organizaciones (AC-52).
2. **Dado** un usuario en la pantalla de ingreso, **cuando** ve las opciones, **entonces** las
   únicas son iniciar sesión con Google o ingresar en modo invitado (AC-21).
3. **Dadas** las organizaciones "Beta" con 8 partidos finalizados, "Alfa" con 3 y "Gamma" con 3
   (y 10 pendientes), **cuando** se consulta el listado, **entonces** el orden es "Beta", "Alfa",
   "Gamma" (AC-33).
4. **Dados** un partido finalizado del 10 de marzo y uno pendiente del 17 de marzo, **cuando** se
   consulta la lista de partidos, **entonces** el del 17 aparece primero como pendiente y el del
   10 segundo como finalizado (AC-16a).
5. **Dado** un usuario invitado o un usuario registrado no miembro, **cuando** lee los
   jugadores/as de una organización, incluso sin pasar por la interfaz, **entonces** recibe su
   apodo y sus estadísticas y nunca su nombre completo ni su fecha de nacimiento (AC-06, AC-24).
6. **Dado** el administrador/a de una organización o un usuario asociado a uno de sus
   jugadores/as, **cuando** ve los jugadores/as, **entonces** ve su nombre completo y su fecha de
   nacimiento (AC-08a, AC-08b).
7. **Dado** un usuario invitado, **cuando** intenta crear una organización, editar un jugador/a o
   crear un partido sin pasar por la interfaz, **entonces** el sistema lo rechaza (AC-49, AC-57,
   AC-58).

También aplican AC-16b, AC-17a, AC-17b, AC-19, AC-85, AC-86 y AC-93.

---

### User Story 3 - Corregir un partido finalizado (Priority: P2)

El administrador/a se equivocó al cargar un partido, o un jugador/a reclama un gol. Corrige
goles, goles en contra, equipos, día o lugar del partido finalizado, sin límite de tiempo, y las
estadísticas de los jugadores/as afectados se recalculan.

**Why this priority**: es la mitigación del principal riesgo del PRD, el desacuerdo sobre los
goles. Sin ella, un error de carga deja estadísticas incorrectas para siempre.

**Independent Test**: finalizar un partido, corregir los goles de un jugador/a y verificar que el
marcador y las estadísticas de todos los afectados quedan como si el partido se hubiera cargado
bien desde el principio.

**Acceptance Scenarios**:

1. **Dado** un partido finalizado 2 a 1 en el que "Juancho" hizo los 2 goles de su equipo y quedó
   con 6 goles, 1 victoria, 0 empates y 0 derrotas, **cuando** el administrador/a corrige sus
   goles a 1, **entonces** el marcador queda 1 a 1 y "Juancho" queda con 5 goles, 0 victorias,
   1 empate y 0 derrotas (AC-51a).
2. **Dado** un partido finalizado que ganó el equipo de "Juancho", sin goles suyos, tras el cual
   quedó con 8 partidos y 3 victorias, **cuando** el administrador/a lo quita del equipo,
   **entonces** queda con 7 partidos y 2 victorias (AC-51b).
3. **Dado** un partido finalizado, **cuando** el administrador/a intenta corregir los goles de un
   jugador/a a -1, **entonces** el sistema lo rechaza y el partido y las estadísticas quedan sin
   cambios (AC-51d).
4. **Dado** un partido finalizado 1 a 1, **cuando** el administrador/a agrega al equipo A un
   jugador/a externo/a "Tano" con 1 gol, **entonces** el marcador queda 2 a 1 a favor del equipo
   A y "Tano" no registra estadísticas (AC-92).
5. **Dado** un usuario registrado que no es administrador/a, **cuando** intenta corregir un
   partido finalizado sin pasar por la interfaz, **entonces** el sistema lo rechaza (AC-05e).
6. **Dado** un administrador/a y un jugador/a con 6 goles, **cuando** intenta cambiarle los goles
   a 100 directamente, sin corregir un partido, **entonces** el sistema lo rechaza y el jugador/a
   sigue con 6 goles (AC-60).

También aplica AC-51c.

---

### User Story 4 - Asociar cuentas a jugadores/as (Priority: P2)

El administrador/a asocia la cuenta de Google de un jugador/a a su ficha indicando su email. Desde
ese momento, esa persona es miembro de la organización y ve los datos completos de sus
compañeros/as. Una misma cuenta puede estar asociada a jugadores/as de distintas organizaciones.

**Why this priority**: convierte a los jugadores/as en miembros. La anotación funciona sin esto,
pero es lo que permite que cada uno vea su organización completa.

**Independent Test**: asociar un email registrado a un jugador/a y verificar que esa cuenta pasa a
ver nombres completos y fechas de nacimiento de esa organización, y de ninguna otra.

**Acceptance Scenarios**:

1. **Dada** una cuenta registrada "pepe@gmail.com" no asociada en la organización y un jugador/a
   sin cuenta, **cuando** el administrador/a asocia ese email a ese jugador/a, **entonces** la
   cuenta queda asociada (AC-28).
2. **Dado** el email "nadie@gmail.com", que no corresponde a ninguna cuenta registrada, **cuando**
   el administrador/a intenta asociarlo, **entonces** el sistema lo rechaza (AC-84).
3. **Dada** una cuenta ya asociada a un jugador/a de la organización, **cuando** el
   administrador/a intenta asociarla a otro jugador/a de la misma organización, **entonces** el
   sistema lo rechaza (AC-12).
4. **Dado** un jugador/a ya asociado a una cuenta, **cuando** el administrador/a intenta asociarle
   otra, **entonces** el sistema lo rechaza (AC-13).
5. **Dada** una cuenta asociada a "Pepe" en la organización A, **cuando** el administrador/a de la
   organización B la asocia a "José", **entonces** el sistema lo permite y cada organización
   muestra a su propio jugador/a con sus propios datos (AC-25).

---

### User Story 5 - Mantener la organización, sus jugadores/as y los partidos pendientes (Priority: P3)

El administrador/a ajusta el nombre y la descripción de la organización, corrige los datos de un
jugador/a y reprograma un partido pendiente o cambia su lugar o sus equipos.

**Why this priority**: es mantenimiento. El flujo principal funciona sin estas ediciones, pero sin
ellas cualquier error de carga obliga a convivir con el dato equivocado.

**Independent Test**: editar cada campo editable y verificar que se guarda el valor nuevo, y que
un usuario no administrador/a no puede hacerlo ni sin pasar por la interfaz.

**Acceptance Scenarios**:

1. **Dada** la organización "Los Martes", **cuando** su administrador/a cambia el nombre a
   "Los Miércoles", **entonces** se guarda "Los Miércoles" (AC-34a). Para la descripción aplica
   AC-34b.
2. **Dado** el jugador/a "Juan Pérez", **cuando** el administrador/a cambia su nombre a
   "Juan Gómez", **entonces** se guarda "Juan Gómez" (AC-18a). Para el apodo y la fecha de
   nacimiento aplican AC-18b y AC-18c.
3. **Dado** un partido pendiente con "Juancho" en el equipo A, **cuando** el administrador/a lo
   pasa al equipo B, **entonces** "Juancho" queda en el equipo B y no en el A (AC-50c). Para el
   lugar y el día aplican AC-50a y AC-50b. Para agregar un externo/a a un pendiente aplica AC-91.
4. **Dado** un usuario registrado que no es administrador/a, **cuando** intenta editar la
   organización, crear o editar un jugador/a, o crear o editar un partido sin pasar por la
   interfaz, **entonces** el sistema lo rechaza (AC-05a a AC-05d, AC-35a, AC-35b).
5. **Dado** un administrador/a, **cuando** ve su organización, **entonces** no existe la opción de
   eliminarla, y si lo intenta sin pasar por la interfaz el sistema lo rechaza (AC-20a, AC-20b).

---

### User Story 6 - Ordenar el ranking de jugadores/as (Priority: P3)

Cualquier persona ordena el listado de jugadores/as de una organización por partidos, goles,
victorias, empates o derrotas para ver quién lidera cada estadística.

**Why this priority**: suma motivación, pero las estadísticas ya son visibles en el orden por
defecto.

**Independent Test**: con jugadores/as de estadísticas distintas, ordenar por cada criterio y
verificar el orden y el desempate.

**Acceptance Scenarios**:

1. **Dados** dos jugadores/as con 5 y 3 goles, **cuando** se ordena por goles, **entonces**
   aparece primero el de 5 (AC-07). Para partidos, victorias, empates y derrotas aplican AC-29 a
   AC-32.
2. **Dados** dos jugadores/as con 5 y 3 partidos, **cuando** se consulta el listado sin elegir
   orden, **entonces** aparece primero el de 5 (AC-87).
3. **Dados** "Tano" y "Beto", ambos con 4 goles, **cuando** se ordena por goles, **entonces**
   "Beto" aparece antes que "Tano" (AC-88).

---

### Edge Cases

- **Organización llena**: con 40 jugadores/as se rechaza crear otro; con 39 se acepta (AC-36,
  AC-37).
- **Mismo jugador/a en los dos equipos**: se rechaza al dar de alta, editar o corregir el partido
  (AC-62, RF-06k).
- **Equipo vacío**: se rechaza un partido con un equipo sin jugadores/as (AC-54).
- **Goles inválidos**: se rechazan -1 y 1,5 goles; se aceptan 0 goles (AC-63 a AC-65).
- **Doble finalización**: se rechaza y las estadísticas no cambian (AC-61).
- **Límite de día en Argentina**: "hoy" y "mañana" se evalúan en la hora oficial de Argentina,
  también cerca de la medianoche, sin importar dónde esté quien usa el sistema.
- **Corrección inválida**: si cualquier validación falla, no cambia ni el partido ni ninguna
  estadística (AC-51d).
- **Textos solo con espacios**: un nombre, apodo, nombre de organización o lugar compuesto solo
  por espacios cuenta como vacío y se rechaza (AC-90).
- **Campos obligatorios vacíos**: se rechazan un jugador/a sin nombre o sin apodo, un externo/a
  sin apodo, una organización sin nombre y un partido sin lugar. Una organización sin descripción
  se acepta (AC-69, AC-70, AC-71, AC-75, AC-78, AC-81).
- **Límites de texto**: 40 caracteres se aceptan y 41 se rechazan; 200 y 201 para la descripción
  de la organización (AC-22a, AC-22b, AC-45, AC-46, AC-68, AC-76, AC-77, AC-79, AC-80, AC-82,
  AC-83).
- **Fechas de nacimiento límite**: se acepta el 1 de enero de 1920 y se rechaza el 31 de diciembre
  de 1919; se acepta hoy y se rechaza mañana (AC-47, AC-48, AC-73, AC-74).
- **Partidos del mismo día**: el dado de alta más tarde aparece primero (AC-93).
- **Empates en los listados**: las organizaciones con los mismos partidos finalizados se ordenan
  alfabéticamente por nombre; los jugadores/as con el mismo valor, por apodo (AC-33, AC-88).
- **Organización sin partidos finalizados**: aparece en el listado, al final del orden.
- **Operaciones fuera de la interfaz**: toda operación no permitida se rechaza aunque se envíe sin
  pasar por la interfaz (RNF-01).
- **Inicio de sesión con contraseña**: se rechaza aunque se envíe directamente al servicio de
  autenticación (AC-56).

## Requirements *(mandatory)*

### Functional Requirements

#### Acceso

- **FR-001**: El sistema MUST permitir iniciar sesión con una cuenta de Google (RF-01a).
- **FR-002**: El sistema MUST registrar al usuario la primera vez que inicia sesión con Google
  (RF-01b).
- **FR-003**: El sistema MUST rechazar cualquier método de inicio de sesión distinto de Google,
  como usuario y contraseña, incluso enviado directamente al servicio de autenticación. La
  pantalla de ingreso MUST ofrecer solo "iniciar sesión con Google" e "ingresar como invitado/a"
  (RF-18, AC-21, AC-56).
- **FR-004**: El sistema MUST permitir ingresar en modo invitado, sin iniciar sesión (RF-04a).
- **FR-005**: El sistema MUST impedir que un usuario en modo invitado cree o edite cualquier
  información (RF-04b).

#### Organizaciones

- **FR-006**: El sistema MUST permitir a cualquier usuario registrado crear una organización con
  un nombre y una descripción opcional (RF-02a, RF-03).
- **FR-007**: El sistema MUST asignar como único administrador/a a quien crea la organización, y
  MUST rechazar la creación de una organización sin administrador/a o con otra cuenta como
  administrador/a (RF-02b, RF-06a, AC-26, AC-59).
- **FR-008**: El sistema MUST permitir solo al administrador/a editar el nombre y la descripción
  de su organización (RF-06e, RF-06f).
- **FR-009**: El sistema MUST impedir que cualquier usuario elimine una organización y MUST NOT
  ofrecer esa opción (RF-17).
- **FR-010**: El sistema MUST mostrar a cualquier usuario, incluido el invitado/a, el listado de
  todas las organizaciones, ordenado por cantidad de partidos finalizados de mayor a menor y, a
  igual cantidad, alfabéticamente por nombre, de a 10 con la opción de ver las 10 siguientes
  (RF-16a, RF-16b).
- **FR-011**: El sistema MUST permitir a cualquier usuario, registrado o invitado/a, llegar a
  cualquier organización desde el listado general de FR-010, para que se vea el uso de la app.
  No hay un acceso directo a "mis organizaciones": un usuario registrado llega a las organizaciones
  de las que es administrador/a o miembro desde ese mismo listado (RF-16a; aclarado el 2026-11-11).

#### Jugadores/as

- **FR-012**: El sistema MUST permitir al administrador/a crear jugadores/as en su organización
  con nombre, apodo y fecha de nacimiento opcional. Todo jugador/a nuevo empieza con 0 partidos,
  0 goles, 0 victorias, 0 empates y 0 derrotas (RF-05, RF-07a, AC-04, AC-72).
- **FR-013**: El sistema MUST rechazar la creación de un jugador/a en una organización que ya
  tiene 40 (RF-07e).
- **FR-014**: El sistema MUST permitir al administrador/a editar el nombre, el apodo y la fecha de
  nacimiento de los jugadores/as de su organización (RF-15).
- **FR-015**: El sistema MUST impedir que quien no es administrador/a de una organización cree o
  edite sus jugadores/as (RF-06c).
- **FR-016**: El sistema MUST mostrar a cualquier usuario, incluido el invitado/a, el listado de
  todos los jugadores/as de una organización (RF-13).
- **FR-017**: El sistema MUST permitir ordenar ese listado por partidos, goles, victorias, empates
  o derrotas, de mayor a menor. El orden por defecto MUST ser por partidos, y los empates MUST
  desempatarse alfabéticamente por apodo (RF-14a, RF-14b, RF-14c).

#### Membresía y privacidad

- **FR-018**: El sistema MUST permitir al administrador/a asociar una cuenta registrada a un
  jugador/a de su organización indicando el email de Google de esa cuenta, y MUST rechazar un
  email que no corresponde a ninguna cuenta registrada (RF-07b, RF-07f).
- **FR-019**: El sistema MUST impedir que una cuenta esté asociada a más de un jugador/a de la
  misma organización y que un jugador/a esté asociado a más de una cuenta (RF-07c, RF-07d).
- **FR-020**: El sistema MUST permitir que una misma cuenta esté asociada a jugadores/as de
  distintas organizaciones, cada uno con sus propios datos (RF-21).
- **FR-021**: Son miembros de una organización su administrador/a y las cuentas asociadas a uno
  de sus jugadores/as. El sistema MUST mostrar a los miembros el nombre completo y la fecha de
  nacimiento de los jugadores/as de esa organización (RF-04c).
- **FR-022**: El sistema MUST mostrar a quienes no son miembros (invitados/as y usuarios
  registrados de otras organizaciones) solo el apodo y las estadísticas de los jugadores/as, y
  MUST NOT entregarles nunca su nombre completo ni su fecha de nacimiento, ni siquiera ante una
  lectura que no pase por la interfaz (RF-04d, AC-06, AC-24).

#### Partidos

- **FR-023**: El sistema MUST permitir al administrador/a dar de alta un partido con día, lugar y
  los jugadores/as de cada equipo elegidos del listado de la organización. El partido queda
  pendiente (RF-08a).
- **FR-024**: El sistema MUST permitir al administrador/a, al dar de alta, editar o corregir un
  partido, agregar a un equipo un jugador/a externo/a indicando solo su apodo (RF-08e).
- **FR-025**: El sistema MUST permitir al administrador/a editar el día, el lugar y los equipos de
  un partido pendiente (RF-06b).
- **FR-026**: El sistema MUST permitir al administrador/a finalizar un partido cargando los goles y
  los goles en contra de cada jugador/a (RF-08b).
- **FR-027**: El sistema MUST sumar cada gol en contra al marcador del equipo rival sin acreditarlo
  a ningún jugador/a (RF-08c).
- **FR-028**: El sistema MUST rechazar la finalización de un partido ya finalizado y la de un
  partido cuyo día es posterior al día actual en Argentina (RF-23, RF-08h).
- **FR-029**: El sistema MUST impedir que quien no es administrador/a de una organización cree,
  edite, finalice o corrija sus partidos (RF-06d).
- **FR-030**: El sistema MUST mostrar a cualquier usuario, incluido el invitado/a, la lista de
  partidos de una organización indicando si cada uno está finalizado o pendiente. La lista MUST
  ordenarse por día del más reciente al más antiguo, desempatar los del mismo día por momento de
  alta del más reciente al más antiguo y mostrarse de a 10 con la opción de ver los 10 siguientes
  (RF-12a, RF-12b, RF-12c).
- **FR-031**: El sistema MUST mostrar los equipos de un partido identificando como "externo/a" a
  todo jugador/a que no pertenece a la organización, y el marcador de los partidos finalizados
  (RF-20a, AC-15, AC-23a, AC-53).

#### Estadísticas

- **FR-032**: Al finalizar un partido, el sistema MUST sumar a cada jugador/a de la organización
  que participó un partido, sus goles y una victoria, un empate o una derrota según el marcador
  de su equipo (RF-09, RF-10, RF-11).
- **FR-033**: El sistema MUST excluir a los jugadores/as externos/as del cómputo de estadísticas y
  MUST sumar sus goles al marcador de su equipo (RF-20b, RF-20c).
- **FR-034**: El sistema MUST permitir al administrador/a corregir, sin límite de tiempo, los goles
  (incluidos los goles en contra), los equipos, el día y el lugar de un partido finalizado
  (RF-06g, RF-06h, RF-06i).
- **FR-035**: Al corregir un partido finalizado, el sistema MUST recalcular las estadísticas de los
  jugadores/as afectados para que reflejen el partido corregido (RF-06j).
- **FR-036**: La corrección MUST aplicar las mismas validaciones que el alta y la finalización. Si
  alguna falla, el partido y todas las estadísticas MUST quedar sin cambios (RF-06k, AC-51d).
- **FR-037**: El sistema MUST impedir que cualquier usuario, incluido el administrador/a, modifique
  las estadísticas de un jugador/a salvo como resultado de finalizar o corregir un partido
  (RF-22).

#### Validaciones

- **FR-038**: El sistema MUST aplicar estas reglas de datos:

  | Dato | Regla | Origen |
  |------|-------|--------|
  | Nombre de jugador/a | Obligatorio, hasta 40 caracteres | RF-19a, RF-19d |
  | Apodo de jugador/a, incluido externo/a | Obligatorio, hasta 40 caracteres | RF-19b, RF-19e |
  | Fecha de nacimiento | Opcional; desde el 1 de enero de 1920 hasta hoy | RF-19c, RF-19f |
  | Nombre de organización | Obligatorio, hasta 40 caracteres | RF-19g, RF-19h |
  | Descripción de organización | Opcional, hasta 200 caracteres | RF-19i |
  | Lugar del partido | Obligatorio, hasta 40 caracteres | RF-19j, RF-19k |
  | Goles y goles en contra | Números enteros mayores o iguales a 0 | RF-08g |
  | Equipos del partido | Al menos 1 jugador/a cada uno; nadie en ambos | RF-08d, RF-08f |

- **FR-039**: El sistema MUST considerar vacío un texto compuesto solo por espacios en los campos
  obligatorios de la tabla anterior (RF-19l).
- **FR-040**: Toda comparación con "hoy" MUST hacerse con el día actual en la hora oficial de
  Argentina (Buenos Aires) (RF-08h, RF-19f).

#### Permisos

- **FR-041**: El sistema MUST validar todos los permisos fuera de la interfaz, de modo que el 100%
  de las operaciones no permitidas se rechacen aunque se envíen directamente, sin pasar por la
  interfaz (RNF-01).

### Key Entities *(include if feature involves data)*

- **Cuenta**: usuario registrado con Google. Se identifica por su email. Puede administrar
  organizaciones y estar asociada a jugadores/as de distintas organizaciones.
- **Organización**: grupo de amigos/as que juega. Tiene nombre, descripción opcional, un único
  administrador/a, hasta 40 jugadores/as y sus partidos.
- **Jugador/a**: integrante de una organización. Tiene nombre, apodo, fecha de nacimiento
  opcional, sus estadísticas (partidos, goles, victorias, empates y derrotas) y, como máximo, una
  cuenta asociada.
- **Partido**: encuentro de una organización. Tiene día, lugar, dos equipos, estado (pendiente o
  finalizado), momento de alta y, una vez finalizado, el marcador.
- **Participación**: presencia de un jugador/a en un equipo de un partido, con sus goles y sus
  goles en contra.
- **Jugador/a externo/a**: participante de un partido que no pertenece a la organización.
  Se identifica solo por su apodo y no acumula estadísticas.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: En cada mes calendario, al menos el 80% de los partidos dados de alta ese mes, sumando
  todas las organizaciones, está finalizado 7 días después del cierre del mes (objetivo del PRD).
- **SC-002**: Dar de alta, finalizar o corregir un partido de 6 contra 6 tarda menos de 2 segundos
  en el 95% de los casos, desde la acción hasta ver el resultado (RNF-02a, RNF-02b, RNF-02c).
- **SC-003**: Crear o editar un jugador/a tarda menos de 2 segundos en el 95% de los casos
  (RNF-03, RNF-04).
- **SC-004**: Con 200 organizaciones, las primeras 10 del listado se ven en menos de 2 segundos en
  el 95% de los casos (RNF-05).
- **SC-005**: En una organización con 200 partidos, los 10 más recientes se ven en menos de
  2 segundos en el 95% de los casos (RNF-06).
- **SC-006**: En una organización con 40 jugadores/as, el listado completo se ve en menos de
  2 segundos en el 95% de los casos (RNF-07).
- **SC-007**: El 100% de las operaciones no permitidas se rechazan, incluidas las enviadas sin
  pasar por la interfaz (RNF-01).
- **SC-008**: En ningún caso un invitado/a o un usuario registrado no miembro recibe el nombre
  completo o la fecha de nacimiento de un jugador/a (RF-04d).
- **SC-009**: En el 100% de los casos, las estadísticas de cada jugador/a coinciden con las que
  resultan de recalcularlas a partir de sus partidos finalizados (RF-09 a RF-11, RF-06j, RF-22).

Los tiempos de SC-002 a SC-006 se miden en el percentil 95, en producción, con emulación de un
teléfono móvil, red 4G rápida y CPU 4 veces más lenta, como define el PRD. Los volúmenes y las
100 mediciones por caso están en AC-38 a AC-44 y AC-89. La aspiración de que
los jugadores/as se sientan incentivados es la visión del producto y el PRD la excluye
explícitamente como métrica.

## Assumptions

- **Fuente**: el PRD está en `PRD.md`, en la raíz del repositorio, no en `docs/PRD.md`.
- **Alcance**: este spec cubre el MVP completo del PRD como una sola feature. Queda fuera todo lo
  listado en "Fuera de Alcance" del PRD, entre otros Elo, votación, interpretación de mensajes de
  WhatsApp, ficha para redes, invitaciones por link, otros deportes, eliminar jugadores/as o
  partidos, gestionar administradores/as, desasociar cuentas y buscar organizaciones.
- **Tamaño de equipos**: el PRD exige al menos 1 jugador/a por equipo y no fija un máximo. No se
  limita la cantidad por equipo; 6 contra 6 es el caso típico.
- **Carga de goles**: el formulario de finalización parte de 0 goles y 0 goles en contra por
  jugador/a, y el administrador/a confirma cada valor. El sistema guarda solo lo que el
  administrador/a confirma.
- **Validaciones de partidos pendientes**: editar un partido pendiente aplica las mismas
  validaciones que el alta (equipos, lugar y apodos de externos/as). Su día puede ser futuro.
- **Jugadores/as externos/as**: no tienen identidad fuera del partido. Dos externos/as con el mismo
  apodo en partidos distintos no se vinculan entre sí.
- **Unicidad**: el PRD no exige apodos ni nombres de organización únicos, por lo que no se valida
  unicidad.
- **Identificación en partidos**: los jugadores/as se identifican por su apodo en los equipos de
  un partido, para cualquier usuario.
- **Email de la cuenta asociada**: lo ven solo el administrador/a de la organización y la propia
  cuenta. No se muestra a otros miembros ni a no miembros.
- **Membresía del administrador/a**: el administrador/a es miembro aunque no esté asociado/a a
  ningún jugador/a. Si juega, se lo/la carga como un jugador/a más; asociarle su propia cuenta es
  opcional y no cambia sus estadísticas.
- **Dependencias**: requiere el inicio de sesión de Google. Las decisiones técnicas (stack,
  almacenamiento, hosting) están en `AGENTS.md` y se resuelven en el plan.
