# Resumen del flujo entre la creación de un archivo localmente hasta subirlo a GitHub
- Antes de usar los comandos git debe añadirse la clave SSH al agente (una vez por sesion).
´´´bash
ssh-add ~/.ssh/[id de la clave]
´´´ 
- Si la clave tiene contraseña, deberá introducirse aqui una sola vez.
- Crear y guardar el archivo en el directorio original del proyecto en local.
- Para sincronizar con GitHub:
 ```bash
 git add .
 git commit -m "Mensaje descriptivo de los cambios realizados"
 git push origin main
 ´´´
 ---
 
 -Para verificar que todo está listo antes de empezar a trabajar
 ´´´bash
 ssh -T git@github.com
 Hi [usuario]! You've succesfully authenticated...
 ´´´
 ---
 Gracias a la autenticación SSH todo funciona sin pedir usuario/contraseña.
 
 ** Ventajas de la configuración actual.
 
 - **Seguridad** : Uso de claves SSH en lugar de contraseñas.
 - **Comodidad** : Gnome gestiona el agente SSH, solo se debe añadir la clave al agente al iniciar sesion en la terminal
 - **Sin conflictos con VSCode** : VSCode usa su propio sistema de autenticación con GitHub en caso de necesitar usarlo. Si quiero usar la terminal con comandos git y editores sencillos, la terminal tiene su propia autenticación.
 
 ---
 
##Ejemplo de uso
 ´´´bash
 ssh-add ~/.ssh/[id dela clave] # Una vez por sessión
 cd [path al directorio local del repositorio]
 nano nueva-nota.md # Crear o editar archivos
 # Sincronizar con GitHub
 git add .
 git commit -m "Comentario"
 git push origin main
 ´´´
 
