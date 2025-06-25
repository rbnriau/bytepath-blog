# 📘 Capítulo 01 – Preparación del Entorno

## 🗂 Estructura del directorio del proyecto local


```plaintext
Servidor_Byte_Path/
├── articulos_html
├── articulos_md
│   ├── cap01_preparacion_entorno.md
│   ├── cap02_seguridad_basica.md
│   ├── cap03_servidor_web_apache.md
│   ├── cap04_contenido_web_estatico.md
│   ├── cap05_organizacion_y_multimedia.md
│   ├── cap06_automatizacion_basica.md
│   ├── cap07_seguridad_y_logs.md
│   ├── cap08_despliegue_en_la_nube.md
│   └── cap09_mantenimiento_y_respaldo.md
├── control
│   └── control_imagenes.ods
├── documentacion
│   ├── bitacora.md
│   └── emojis.md
├── herramientas
├── imagenes
│   ├── optimizadas
│   └── originales
│       └── bit-01.png
├── logs
└── portada
```




> 📌 **Recordatorio:** Explicar el porqué de esta estructura

## 🖥 Instalación de Debian 12  

Usaré un mini-PC para el entorno de pruebas, una vez todo funciones correctamente se migrará a una instancia virtual en la nube, GCP o AWS.



La instalación del sistema se realizó desde un USB, con Debian 12 sin entorno de escritorio. Una vez completada, lo primero fue poner el entorno en marcha:

1. Actualización inicial del sistema.

2. Instalación de sudo y adición del usuario rubenrv al grupo de administradores:


<pre>apt install sudo
usermod -aG sudo rubenrv</pre>


3. Tras reiniciar, verifiqué con id rubenrv que el cambio se aplicó correctamente.

4. Luego instalé el servidor SSH (openssh-server) y comprobé que estuviera activo y funcionando.


<pre>sudo systemctl status sshd</pre>

> 💡 Todo listo para administrar el sistema de forma remota.



## 👤 Usuario, hostname y red

**Hostname:** 1-bytepath  
**Usuario:** rubenrv  
**IP:** 192.168.8.150/24  
**Red Local** 192.168.8.0/24  


## 🔐 Acceso remoto con SSH

Una vez verificado que el servicio SSH está activo y permite conexiones, es momento de reforzar la seguridad del sistema. Para ello, configuraremos el acceso únicamente mediante clave pública y desactivaremos el acceso por contraseña.

1. Generar clave SSH (desde el cliente)  

[Generar clave](../imagenes/originales/cap01_ssh_keygen_01.png)  

-t ed25519: indica que se usará el algoritmo de clave ed25519.  
-C "rubenrv@bytepath": es un comentario opcional para ayudar a identificar la clave.  
Este comando creará dos archivos, la clave privada que no se compartirá nunca y la pública que copiaremos en el servidor.

2. Copiar la clave pública en el servidor  

[Copiar clave](../../Servidor_Byte_Path/imagenes/originales/cap01_ssh_copyID_02.png)  


Esto creará el archivo ~/.ssh/authorized_keys en el servidor (si no existe) y añadirá la clave pública.  

3. Comprobar el acceso con la clave y sin pass  

[Comprobar clave](../imagenes/originales/cap01_chek-key_03.png)

4. Eliminar acceso al servidor con contraseña.  

[Editar ssd_config](../imagenes/originales/cap01_sshdconfig_04.png)

Con esta configuración evitamos que se nadie pueda intentar conectarse con una pass. La única opción válida es usar el equipo qeu tenga la clave privada.  

Por último reiniciamos el servicio ssh y comprobamos el estado del servicio.  

[Reiniciar servicio ssh](../imagenes/originales/cap01_restart_ssh_05.png)  

> 📌 **Nota:**  
> En Debian y derivados, el servicio del servidor SSH se llama `ssh`.  
> Sin embargo, `sshd` también funciona como alias y ambos comandos reinician el servicio correctamente:  
> 
> ```bash
> sudo systemctl restart ssh
> sudo systemctl restart sshd
> ```
> 
> Esto puede variar en otras distribuciones, donde `sshd` suele ser el nombre más común del servicio.



## 📦 Paquetes básicos

Una vez completada la instalación de Debian 12 y configurado el usuario principal, se procede a instalar una serie de herramientas esenciales que facilitarán la administración del servidor y prepararán el entorno para tareas futuras. Algunas herramientas ya se han utilizado antes como **sudo** u **openssh-server**.  


### 🔹 Actualización del sistema

```bash
sudo apt update && sudo apt upgrade -y
```

### 🔹 Paquetes de administración

```bash
sudo apt install sudo net-tools curl wget htop bash-completion
```
> 📌 **sudo:** para otorgar permisos administrativos.  
> 📌 **net-tools:** utilidades como ifconfig.  
> 📌 **curl y wget:** herramientas para descargar archivos desde la terminal.
> 📌 **htop:** monitor interactivo de procesos.  
> 📌 **bash-completion:** autocompletado útil en terminal. 
> 📌 **lm-sensors:** útil para monitorear la salud del equipo.  
> 📌 **fancontrol:** permite controlar la velocidad de los ventiladores.  
> 📌 **strace:** rastrea las llamadas al sistema realizadas por un proceso.  



### 🔹 Acceso remoto por SSH

```bash
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh  
```

### 🔹 Servidor web Apache (instalación)

```bash
sudo apt install apache2
```
La configuración del servidor web se tratará en el **Capitulo 03-Servidor Web Apache.  


## ✅ Cierre del capítulo

📌 **Resumen rápido**  
- Instalación mínima de Debian 12 sin entorno gráfico.  
- Configuración de acceso remoto vía SSH con autenticación con clave.  
- Estructuración del entorno de trabajo y directorios del proyecto.  
- Instalación de herramientas administrativas básicas para monitorización y gestión del sistema.  


🔧 **Estado actual del entorno**  
El servidor local está operativo, accesible por SSH, con un entorno organizado y preparado para comenzar con la configuración de seguridad.  


🚀 **¿Y ahora qué?**  
En el próximo capítulo se abordarán las medidas básicas de protección como configurar cortafuegos.

💬 **Bitácora del viajero**  
> *"No hay mejor forma de conocer un sistema que ensuciarse las manos desde el principio. Las bases están listas. Toca protegerlas."*  



