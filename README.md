
## Usuarios y administradores (visibilidad, no seguridad)

Todos los dispositivos usan el mismo token de lectura y escritura. Los permisos son de la propia app y sirven para saber quién hace qué; cualquiera con el token podría editar `data.json` a mano.

- Al guardar token y ruta, la app pregunta **quién eres** (una persona del equipo). Cámbialo en *Equipo → Cambiar*.
- **Administrador**: puede crear, editar y asignar todo, gestionar el equipo y dar o quitar el rol a otros (*Hacer admin / Quitar admin*). Quien cambia de usuario a uno que es administrador debe poner la contraseña.
- **Usuario**: puede crear tareas para sí mismo con fecha de inicio de hoy (fija). En el seguimiento de cualquier tarea solo puede añadir o editar líneas de hoy y de mañana. En sus propias tareas también puede **finalizarlas, pausarlas o reabrirlas**. En las demás solo toca ese seguimiento. No cambia nada más.
- **Primer administrador**: en el selector, elige tu usuario y pulsa *Administrador…*. Si aún no hay contraseña, la app te pide crearla. No está escrita en el código: se guarda como hash (PBKDF2 con sal) dentro de `data.json`, en el repositorio privado. Cámbiala en *Equipo → Contraseña admin*.
- Todos los dispositivos deben tener la misma versión: las versiones antiguas borrarían la contraseña y los administradores al guardar.
