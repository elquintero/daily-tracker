# Daily Tracker (beta)

PWA sin servidor: la app se publica en GitHub Pages y los datos viven en un `data.json` dentro de un repositorio **privado**, que la propia app lee y modifica con la API de GitHub.

## Puesta en marcha

1. **Repositorio de la app** (público, para usar GitHub Pages gratis): crea `daily-tracker` y sube `index.html`, `sw.js`, `manifest.webmanifest`, `icon.svg`, `icon-192.png` e `icon-512.png`. En *Settings → Pages* elige la rama `main` y la carpeta `/ (root)`. La URL será `https://TU_USUARIO.github.io/daily-tracker/`.
2. **Repositorio de datos** (privado): crea `daily-tracker-data` y sube `data.json`. No uses el mismo repositorio que la app: cada guardado haría un commit y Pages se reconstruiría cada vez, y además los datos del equipo quedarían públicos.
3. **Token**: *GitHub → Settings → Developer settings → Personal access tokens → Fine-grained tokens → Generate*.
   - Repository access: *Only select repositories* → `daily-tracker-data`.
   - Permissions → Repository permissions → **Contents: Read and write**.
   - Pon una caducidad (por ejemplo 90 días) y renueva el token cuando caduque.
4. Abre la URL de Pages, pulsa **Equipo → Configurar** (o el indicador de sincronización del menú lateral) y rellena `usuario/daily-tracker-data` y el token. Repite en cada dispositivo.
5. Instálala desde el navegador: *Instalar app* en Chrome/Edge o *Añadir a pantalla de inicio* en móvil.

## Cómo funciona

- Cada cambio se guarda en el navegador y, a los 0,8 s, se envía como commit a `data.json`. El historial de commits sirve de copia de seguridad.
- La app lee el fichero al abrirla, al volver a la pestaña y cada 60 s.
- Si dos personas guardan a la vez gana la primera. La segunda ve el aviso de conflicto, la app recarga los datos y hay que repetir el cambio.
- El token se guarda solo en el navegador de cada dispositivo (localStorage). Usa un token limitado al repo de datos y no lo compartas.
- Sin conexión se puede consultar y editar; los cambios se envían al volver la red.

## Actualizar la app

Sube los ficheros nuevos y cambia `V='dt-v2'` por `dt-v2` en `sw.js` para que los dispositivos descarguen la versión nueva.
