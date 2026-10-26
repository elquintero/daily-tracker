# Daily Tracker (beta)

PWA sin servidor: la app se publica en GitHub Pages y los datos viven en un `data.json` dentro de un repositorio **privado**, que la propia app lee y modifica con la API de GitHub.

## Puesta en marcha

1. **Repositorio de la app** (público, para usar GitHub Pages gratis): crea `daily-tracker` y sube `index.html`, `sw.js`, `manifest.webmanifest`, `config.json`, `icon.svg`, `icon-192.png` e `icon-512.png`. En `config.json` pon el repositorio de datos (`usuario/daily-tracker-data`); no es un secreto, solo el nombre. En *Settings → Pages* elige la rama `main` y la carpeta `/ (root)`. La URL será `https://TU_USUARIO.github.io/daily-tracker/`.
2. **Repositorio de datos** (privado): crea `daily-tracker-data` y sube `data.json`. No uses el mismo repositorio que la app: cada guardado haría un commit y Pages se reconstruiría cada vez, y además los datos del equipo quedarían públicos.
3. **Token**: *GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate*.
   - Repository access: *Only select repositories* → `daily-tracker-data`.
   - Permissions → Repository permissions → **Contents: Read and write**.
   - Pon una caducidad (por ejemplo 90 días) y renueva el token cuando caduque.
4. Abre la URL de Pages, pulsa **Equipo → Configurar** (o el indicador de sincronización del menú lateral) y pega tu token. El repositorio de datos ya viene de `config.json`, así que solo un administrador identificado ve el campo *Repositorio* (para cambiarlo en su dispositivo). Repite en cada dispositivo.
5. Instálala desde el navegador: *Instalar app* en Chrome/Edge o *Añadir a pantalla de inicio* en móvil.

## Cómo funciona

- Cada cambio se guarda en el navegador y, a los 0,8 s, se envía como commit a `data.json`. El historial de commits sirve de copia de seguridad.
- La app lee el fichero al abrirla. Después, al volver a la pestaña y cada 60 s, solo comprueba si alguien ha cambiado algo: si es así muestra un aviso discreto («Hay cambios nuevos») con *Actualizar* y *Ahora no*, sin tocar lo que estás viendo. Al abrir una ficha para editar, la app carga antes los cambios pendientes.
- Si dos personas guardan a la vez gana la primera. La segunda ve el aviso de conflicto, la app recarga los datos y hay que repetir el cambio.
- El token se guarda solo en el navegador de cada dispositivo (localStorage). Usa un token limitado al repo de datos y no lo compartas.
- Sin conexión se puede consultar y editar; los cambios se envían al volver la red.
- Si dejas la app abierta y cambia el día, lo detecta y se actualiza sola (calendario, fechas y contadores de días).

## Usuarios y administradores (visibilidad, no seguridad)

Todos los dispositivos usan el mismo token de lectura y escritura. Los permisos son de la propia app y sirven para saber quién hace qué; cualquiera con el token podría editar `data.json` a mano.

- Al guardar token y ruta, la app pregunta **quién eres** (una persona del equipo). Cámbialo en *Equipo → Cambiar*.
- **Contraseña personal**: al elegir un usuario la app pide su contraseña; la primera vez la crea la propia persona (mínimo 4 caracteres). Se guarda como hash dentro de `data.json`. Si alguien la olvida, un administrador usa *Restablecer clave* en *Equipo* y la persona crea otra la próxima vez. Un usuario ya identificado en un dispositivo no la vuelve a escribir hasta que cambie de usuario.
- **Administrador**: puede crear, editar y asignar todo, gestionar el equipo. Solo el **administrador principal** da o quita el rol de administrador a otras personas (*Hacer admin / Quitar admin*), y la persona debe haber iniciado sesión al menos una vez.
- **Usuario**: puede crear tareas para sí mismo con fecha de inicio de hoy (fija). En sus propias tareas puede **finalizarlas, pausarlas o reabrirlas** y añadir o editar líneas de seguimiento de hoy y de mañana. Las tareas de otras personas las ve en solo lectura, sin botón Guardar.
- **Primer administrador**: en el selector, elige tu usuario y pulsa *Administrador…*. Si aún no hay contraseña de administrador, la app te pide crearla. Quien la crea pasa a ser el **administrador principal**; a partir de entonces el botón *Administrador…* deja de mostrarse y solo el principal puede crear más administradores. No está escrita en el código: se guarda como hash (PBKDF2 con sal) dentro de `data.json`, en el repositorio privado. Cámbiala en *Equipo → Contraseña admin*.
- Todos los dispositivos deben tener la misma versión: las versiones antiguas borrarían la contraseña y los administradores al guardar.

## Rol de la sesión (opcional)

En *Equipo → Configurar* existe un selector *Rol* (administrador o invitado de solo lectura, pensado para un token de solo lectura). Por ahora solo lo ve un administrador, igual que los campos *Fichero* y *Rama*, y se conservan por si se usan en el futuro. Quien tenga un token de solo lectura no puede guardar: la app lo avisa.

## Actualizar la app

Sube los ficheros nuevos al repositorio de la app. El service worker usa la red primero, así que con conexión se carga siempre la versión más reciente; la copia en caché solo sirve sin conexión. Cambia `V='dt-vNN'` en `sw.js` en cada versión para limpiar cachés antiguas. El pie de página muestra la versión instalada a todos; el registro de novedades, que se abre al pulsarlo, solo lo ve un administrador.

## Quién está conectado

En *Equipo* cada persona tiene un punto **verde** si está conectada ahora (con sesión iniciada) y gris si no, con «visto hace…». Cada dispositivo con sesión iniciada y la pestaña visible escribe un latido en `presence.json` (en el repositorio de datos) cada 3 minutos; se considera conectado quien ha dado señal en los últimos 7 minutos. Cada latido es un commit pequeño de ese fichero, así que el historial del repositorio de datos crecerá; el fichero se crea solo.

## Daily (sorteo)

El menú *Daily* sustituye al script de Python: sortea el orden de intervención y muestra una persona cada vez («3 de 10») con el botón *Siguiente* (también con Espacio, Enter o →; ← vuelve atrás; Esc sale). Al llegar al final ofrece volver a sortear. Solo lo ve el **administrador principal**: quien creó la contraseña de administrador (se puede transferir con *Hacer principal* en *Equipo*; si la contraseña se creó con una versión anterior y no consta quién fue, lo ven todos los administradores hasta que uno pulse *Hacer principal*). El principal marca quién participa; quien tiene una baja o vacaciones activas queda excluido automáticamente. Opcionalmente muestra las tareas de la persona que habla.

## Estadísticas: Actual y Consultas

*Estadísticas* tiene dos pestañas. **Actual** es el resumen de siempre. **Consultas** busca en todo el histórico de tareas (también sin tarea, bajas y vacaciones) por texto (título, descripción, seguimiento o persona, sin distinguir tildes), persona (o sin asignar), estado, tipo, prioridad, un rango de fechas sobre inicio, fin, creación o fin estimado, y tareas pasadas de su fecha estimada. Permite ordenar y **exportar el resultado a CSV** (abre bien en Excel). Pulsa el título para abrir la tarea.

## Reuniones

*Tipo de tarea → Reunión*: fecha, hora, asistentes (varias personas) y si es **interna** o **con el Ministerio**. Se muestra con un icono de calendario tipo Outlook (azul si es interna, morado si es con el Ministerio) en la tarea de cada asistente. Se cierra sola **12 horas después de su hora de inicio**. El menú *Reuniones*, bajo Backlog, lista las de hoy, las próximas y las anteriores, y su icono cambia de color cuando hay reuniones abiertas hoy en el equipo (gris: ninguna; azul: solo internas; morado: alguna con el Ministerio), con un contador. Solo los administradores crean y editan reuniones; el resto las ve en solo lectura.
