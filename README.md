
## Roles: administrador e invitado

La seguridad real la pone GitHub, no la app: lo que decide quién puede escribir es el token.

- **Administrador**: token fine-grained sobre `daily-tracker-data` con *Contents: Read and write*. En *Equipo → Configurar* deja el rol en *Administrador*.
- **Invitado**: crea otro token fine-grained, también solo sobre `daily-tracker-data`, pero con *Contents: Read-only*. En *Equipo → Configurar* elige el rol *Invitado*. La app oculta añadir, editar, arrastrar y eliminar, y las fichas se abren en solo lectura.
- Aunque un invitado manipule la app, GitHub rechaza cualquier escritura con su token. Puedes dar un token distinto a cada invitado y revocarlo individualmente en *Settings → Developer settings → Fine-grained tokens*.
