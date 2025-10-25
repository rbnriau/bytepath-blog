# Capítulo 02 – Seguridad Básica

## Introducción a la Seguridad en Servidores Web

 Cuando se trata de configurar un servidor web, la seguridad es uno de los aspectos más importantes a considerar. Un servidor web expuesto a internet puede ser vulnerable a diversos tipos de ataques, como intrusiones, denegación de servicio, robo de información, entre otros. Por lo tanto, es crucial implementar medidas de seguridad sólidas desde el inicio para proteger nuestra infraestructura y los datos que aloja.
 
 1. **Actualización del sistema operativo y software:** Fundamental para corregir vulnerabilidades conocidas y evitar que sean explotadas por atacantes.
 2. **Confguración segura del servidor web:** Ajustar permisos de archivos, puertos abiertos y módulos habilitados puede ayudar a reducir la superficie de ataque.
 3. **Gestión de cuentas y permisos:** Establecer políticas de contraseñas seguras, limitar el acceso a cuentas privilegiadas y otorgar permisos mínimos a los usuarios para prevenir accesos no autorizados.
 4. **Monitorización y registro de actividad:** Configurar un sistema de registro eficaz y monitorizar regularmente los registros puede ayudarnos a detectar y responder a posibles incidentes de seguridad.
 5. **Uso de herramientas de seguridad:** Firewalls, IDS y herramientas  de escaneo de vulnerabilidades pueden complementar nuestra seguridad.
 
 En este capítulo, nos enfocaremos en la instalación y configuración de UFW(Uncomplicated Firewall) que nos ayudará a establecer reglas de seguridad básica para el servidor web.
 
## UFW
### Instalación de UFW

Actualizamos el sistema e instalamos ufw.
```bash
sudo apt update && sudo apt upgrade -y
sudo apt install openssh-server
```
Antes de habilitar UFW debemos asegurarnos de que el puerto 22 esté abierto o corremos el riesgo de que el firewall deniegue nuestra actual conexión ssh, lo que nos dejaría sin posibilidad de acceder remotamene al equipo.

```bash
sudo ufw allow 22/tcp
sudo ufw status numbered
```
Habilitamos ufw
```bash
sudo ufw enable
```

Tal como nos advierte la salilda del comando, 



![regla permitir ssh](../imagenes/A1C2N03.png)

Y ahora si, lo habilitamos.

![habilitar ufw](../imagenes/A1C2N04.png)

Y podemos comprobar que el puerto 22 está escuchando mi conexión.

![netstat](../imagenes/A1C2N05.png)


### Reglas mínimas 

Es el momento de configura unas reglas mínimas. Permitiré el acceso remoto ssh, que ya configuramos para que solo permita conectarse con mi clave privada,  permitiremos el 80 para HTTP y 443 para HTTPS.

![reglas ufw](../imagenes/A1C2N06.png)

También necsito el sevicio SFTP para transmitir archivos pero usa el mismo puerto de SSH.

Con netstat podemos ver los puertos abiertos actualmente.

![netstat](../imagenes/A1C2N07.png)




### Verificación del estado

Estado general de firewall

![status](../imagenes/A1C2N08.png)

Reglas específicas

![reglas añadidas](../imagenes/A1C2N09.png)

## Fail2ban  

### Introducción a Fail2ban

Fail2ban es una herramienta que actúa como sistema de detección y prevención de intrusiones protegiendo nuestro servidor  contra:

1. Ataques de fuerza bruta contra SSH.
2. Scans maliciosos.
3. Intentos de explotación de vulnerabilidades.

### Instalación básica

![Instalación de Fail2ban](../imagenes/A1C2N10.png)
![Activación de Fail2ban](../imagenes/A1C2N11.png)


### Configuración personalizada

Este paso es importante hacerlo para que cuando se actualice Fail2ban no se sobreescriba nuestra configuración. Creando el fichero jail.local, fail2ban tendrá preferencia por eta configuración en lugar de jail.conf
- Tuve problemas porque no tuve en cuenta que Debian 12 no guarda los log de ssh como espera fail2ban que sería en /var/log/auth.log sino que Debian usa systemd-journald como sistema de logs por defecto. Esto provocaba errores y hay que configurarlo especialmente tal como mostraré en las siguientes secciones.


![Crear nuevo archivo jail.local](../imagenes/A1C2N12.png)

Aquí lo personalizamos para SSH, podemos crear nuevas Jails para otros servicios como apache2.

### Habilitar y arrancar el servicio

![Habilitar Fail2ban](../imagenes/A1C2N13.png)




### Verificar que la jail sshd está activa

![jail activas](../imagenes/A1C2N14.png)

![ estado jail sshd](../imagenes/A1C2N15.png)

 


### Protección para Apache

Si nuestro servidor web tiene un sitio público, Apache está constantemente recibiendo trçafico como:
1. Bots maliciosos.
2. Scanners automáticos.
3. Fuerza bruta contra formularios de login.
4. Exploits conocidos contra rutas como /phpmyadmin, /wp-login.php, etc.

Aunque de momento estamos en una red local, voy a ir preparándome para estar listo en el momento de migrar a internet.

Crearé tres jails para apache en mi archivo jail.local.

1. 🔹 apache-auth
    -  Detecta múltiples errores de autenticación HTTP.
    -  Protege paneles admin protegidos por .htaccess, por ejemplo.
2. 🔹 apache-noscript
    -  Detecta intentos de acceder a scripts no permitidos (común en ataques automáticos).
3. 🔹 apache-badbots
    -  Detecta bots que usan User-Agents sospechosos conocidos por comportamiento malicioso.
  

![Crear jails Apache](../imagenes/A1C2N16.png)

![Estado Fail2ban](../imagenes/A1C2N17.png)

### Monitorización y gestión

1. Monitoreo básico de UFW
2. 
   ![Puertos abiertos](../imagenes/A1C2N18.png)
   ![Jail activas](../imagenes/A1C2N19.png)
   

### Pruebas de funcionamiento

Tengo una  maquina virtual Kali que usaré para intentar loguearme repetidas veces hasta que Fail2ban la bloquee. Con esto comprobaremos que no pueden hacer intentos indefinidos de logeo a mi servidor.

![intento de acceso SSH](../imagenes/A1C2N20.png)

![logs intentos de acceso y baneo](../imagenes/A1C2N21.png)

Sobre la protección a Apache2, de momento no voy a hacer más pruebas porque aiendo una web estática sin formularios y logins, no tiene sentido probar protecciones a esas características.
 Sin embargo habría que añadir alguna configuración más a la seguridad básica.
 1. **Eliminar o sustituir página por defecto de Apache**
 
 ![Cambiar página por defecto de apache](../imagenes/A1C2N22.png)
 
 2. **Configurar Apache para que no muestre info de su versión**
 
 ![Configuración apache](../imagenes/A1C2N23.png)


## Notas para producción

Cuando migre el servidor web a la nube debería cambiar el puerto 22 por defecto para ssh por otro.
Debo recordar que GCP tiene su propio firewal, deberé informarme a fondo de esto pues podría bloquearme sin querer.
En entornos Cloud añadir mi IP pública a **ignoreip** y configurar optimizaciones.
De momento no he configurado Apache para generar tráfico cifrado HTTPS
 

## Cierre del capítulo

**Resumen rápido**
- Implementamos UFW como cortafuegos básico para el servidor.
- Configuramos reglas mínimas de seguridad (SSH, HTTP, HTTPS).
- Verificamos el estado del firewall y puertos abiertos.
- Aprendimos sobre consideraciones clave para entornos de producción.
- Instalamos y configuramos Fail2ban y comprobamos su funcionamiento.

**Estado actual del entorno**
El servidor ahora tiene:
- Firewall básico activado (UFW) con puertos esenciales abiertos.
- Fail2ban instalado y configurado.
- SSH configurado solo con autenticación por clave pública.
- Servicios web (HTTP/HTTPS) accesibles pero protegidos.


**¿Y ahora qué?**
En el próximo capítulo nos enfocaremos en la configuración de apache2.


**Bitácora del viajero**
> *"La seguridad no es un producto, sino un proceso. Hoy hemos puesto los primeros cimientos de un servidor resistente, pero el camino continúa. Como dijo Bruce Schneier: 'La seguridad es siempre una compensación entre conveniencia y protección'. Encontremos ese equilibrio."


