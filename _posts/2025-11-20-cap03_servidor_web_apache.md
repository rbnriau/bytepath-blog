---
title: "Título del artículo"
date: 2025-11-20
---
# 🌐 Capítulo 03 – Servidor Web Apache

En este capítulo profundizaré en la configuración de Apache2 en nuestro entorno local de pruebas.
Recordemos la configuración de red:
- Hostname del servidor: 1-bytepath
- Dirección IP del servidor: 192.168.8.150/24 (estatica)
- Red local: 192.168.8.0/24
En el capítulo 1 ya instalamos Apache pero antes de empezar nos aseguramos de que Apache esté funcionando correctamente.
1. Comprobar el estado del servicio:
![comprobar](../imagenes/A1C3N01.png)
2. Acceder a la página web por defecto de Apache2:
![acceder](../imagenes/A1C3N02.png)
Recordemos que en el capítulo anterior eliminamos la pagina web por defecto de Apache por seguridad.


## 📁 Estructura de directorios y archivos de configuración de Apache2.

- <pre><code> /etc/apache2/</code></pre> Directorio principal de configuración de Apache2.
- <pre><code>/etc/apache2/apache2.conf</code></pre> El archivo de configuración principal. Contiene directivas globales.
- <pre><code>/etc/apache2/ports.conf</code></pre>Define los puertos en los que Apache2 escucha (por defecto 80 para HTTP y 443 para HTTPS).
- <pre><code>/etc/apache2/mods-available/</code></pre> Contiene enlaces simbólicos a los módulos que están actualmente habilitados.
- <pre><code>/etc/apache2/conf-available/</code></pre>Contiene archivos de configuración que no son módulos ni Virtual Hosts, pero que se pueden habilitar.
- <pre><code>/etc/apache2/conf-enabled/</code></pre>Contiene enlaces simbólicos a las configuraciones que están actualmente habilitadas.
- <pre><code>/etc/apache2/sites-available/</code></pre> Contiene archivos de configuración para los Virtual Hosts disponibles (sitios web).
- <pre><code>/etc/apache2/sites-enabled/</code></pre> Contiene enlaces simbólicos a los Virtual Hosts que están actualmente habilitados.
- <pre><code>/var/www/html/</code></pre>El directorio por defecto para los archivos del sitio web.
**Comandos útiles**
- <pre><code>a2enmod </code></pre> Habilita un módulo.
- <pre><code>a2dismod</code></pre> Deshabilita un módulo.
- <pre><code>a2ensite</code></pre>Habilita un Virtual Host.
- <pre><code>a2dissite</code></pre>Deshabilita un Virtual Host.
- <pre><code>a2query -m</code></pre> Lista los módulos habilitados.
- <pre><code>apache2ctl configtest</code></pre>Comprueba la sintaxis de los archivos de configuración antes de reiniciar Apache.

## 🔧 Creación de un virtual host para el sitio web

En lugar de usar el directorio por defecto (/var/www/html) crearemos un Virtual Host para nuestro sitio web, esto nos permite alojar varios sitios en el mismo servidor si fuese necesario en el futuro.
1. Crear el directorio raíz  
 
    ![Raiz](../imagenes/A1C3N03.png)

2. Asignar permisos adecuados al directorio.

   ![asignar](../imagenes/A1C3N04.png)

3. Crear una página de prueba.(copio la que tenemos por defecto)
 
   ![crear](../imagenes/A1C3N05.png)

4. Y la edito, se quedará así de momento hasta que diseñe la portada del sitio web.


   ![editar](../imagenes/A1C3N06.png)

**Configurar el Virtual Host**

![conf](../imagenes/A1C3N07.png)

Habilitar el nuevo sitio.

![enable](../imagenes/A1C3N08.png)

Verificamos y reiniciamos apache

![verificar](../imagenes/A1C3N09.png)

Deshabilitamos el sitio por defecto. Ya que apunta al mismo puerto, debemos deshabilitarlo para que tome el nuevo host virtual en el el puerto 80

![disable](../imagenes/A1C3N10.png)

![comprobar](../imagenes/A1C3N11.png)

Comprobamos que la página por defecto ahora es la del nuevo host virtual.

![portada](../imagenes/A1C3N12.png)

**Explicación de las directivas importantes**

- <pre><code>DocumentRoot</code></pre> Define la Raíz del documento para este Host virtual.
- <pre><code>ServerName</code></pre> El nombre de dominio o hostname que Apache usará para identificar este Virtual Host.
- ![bloque](../imagenes/A1C3N13.png)
-  Este bloque configura opciones específicas para el directorio del sitio.
   - <pre><code>Options Indexes FollowSymLinks MultiViews</code></pre> Habilita indexacción de directorios, seguimiento de enlaces simbólicos y negociación de contenido. 
     - 📌 La opción Indexes es recomendable quitarla en producción, más adelante se explica el porqué de esta medida.
   - <pre><code>AllowOverride All</code></pre> Permite el uso de archivos .htaccess para anular configuraciones, autenticación, redirecciones. 
     - 📌 Este fichero .htaccess lo usaremos cuando configuremos Apache para dirigir tráfico HTTPS.
   - <pre><code>Require all granted</code></pre> Permite el acceso desde cualquier dirección IP.


## ✅ Habilitación de Módulos comunes y útiles
Apache está diseñado modularmente lo que nos permite habilitar o deshabilitar funcionalidades específicas.
Esto nos permite mantener un servidor ligero y seguro al cargar sólo lo necesario.
1. Listar módulos disponibles y habilitados
   
Listado de módulos disponibles:
![listar modulos](../imagenes/A1C3N14.png)

Listado de módulos actualmente habilitados en el servidor:
![habilitados](../imagenes/A1C3N15.png)

2. Módulos útiles y esenciales para un blog

 - <pre><code>mod_headers</code></pre>
      - Este módulo permite manipular cabeceras HTTP que Apache envía en sus respuestas.
útil para configurar cabeceras de seguridad que mejoran la protección del sitio contra varios tipos de ataques
      - <pre><code>sudo a2enmod headers</code></pre>
 - <pre><code>mod_deflate</code></pre>
      - Habilita la compresión de contenido (GZIP) para archivos como HTML,CSS y JavaScript. Esto reduce el tamaño de los datos que se envían al navegador del ususario, lo que resulta en tiempos de carga más rápidos y un menor consumo de ancho de banda. 
      - <pre><code>sudo a2enmod deflate</code></pre>
      - <pre><code>sudo systemctl restart apache2</code></pre>
      - Para configurarlo creamos un archivo de configuración específico:
      - <pre><code>sudo nano /etc/apache2/conf-available/deflate.conf</code></pre>
      - ![deflate.conf](../imagenes/A1C3N16.png)
      - Y habilitamos esta configuración:
      - <pre><code>sudo a2enconf deflate.conf</code></pre>
      - ![enable deflate](../imagenes/A1C3N17.png)
 - <pre><code>mod_expires</code></pre>
      -  Este módulo permite añadir cabeceras 'Expires' y 'Cache-Control' a los archivos estáticos e indica al navegador del usuario durante cuánto tiempo puede almacenar en caché estos archivos evitando que el navegador los solicite de nuevo en visitas posteriores, reduciendo la carga del servidor y mejorando drásticamente la velocidad de carga para visitantes recurrentes.
      -  📌 Podemos configurarlo en el mismo archivo de configuración del virtual host pero prefiero hacer una archivo de configuración específico para este módulo, tal como hicimos con el módulo deflate.
      -  📌 Este asunto es complejo y puede tener consecuencias si no se comprende bien:
         - La habilitación de un módulo es 'global', por tanto, todos los sitios incluidos en el servidor se verán afectados.
         - Los módulos necesitan una configuración, no la tienen por defecto, por tanto, cualquier módulo habilitado sin configuración no servirá de nada.
         - La configuración de los módulos pueden incluirse en varios sitios:
           - En su propio archivo de configuración como hemos hecho anteriormente.(configuración global, afecta a todos los sitios)
           - Dentro del archivo de configuración del Virtual Host(afectará a ese sitio solamente). Por tanto, si configuramos el módulo dentro del Virtual Host, la configuración no afectará a los demás sitios que aunque esté activado no disponen de una configuración y no servirá de nada el que esté activado.
      -  Habilitamos mod_expires:
      -  <pre><code>sudo a2enmod expires</code></pre>
      -  <pre><code>sudo systemctl restart apache2</code></pre>
      -  Creamos el archivo de configuración para mod_expires:
      -  <pre><code>sudo nano /etc/apache2/conf-available/expires.conf</code></pre>
      -  ![Crear conf](../imagenes/A1C3N18.png)
      -  Y habilitamos la nueva configuración:
      -  <pre><code>sudo a2enconf expires.conf</code></pre>
      -  Verificamos la sintaxis y reiniciamos el servicio.
      -  <pre><code>sudo apache2ctl configtest</code></pre>
      -  <pre><code>sudo systemctl restart apache2</code></pre>

## 🔐 Configuración de Seguridad Adicional en Apache2.
   
   
1. **Deshabilitar la visualización de la versión de Apache y del sistema operativo:**
   Como se explicó en el capítulo anterior sobre seguridad básica, es crucial evitar que Apache revele su versión y el sistema operativo, ya que esta información puede ser valiosa para posibles atacantes.
   Ya configuramos estas directivas en el archivo /etc/apache2/conf-available/security.conf.
     
![Crear conf](../imagenes/A1C2N23.webp)

2. **Configurar opciones para el directorio raíz:**
   Ya se comentó anteriormente, debería eliminarse la opción 'Indexes' para prevenir que en caso de error un atacante no pueda acceder a un listado de directorios del sitio web.

[Crear conf](../imagenes/A1C3N19.webp)

3. **Configurar cabeceras de seguridad con mod_headers**
<pre><code>sudo a2enmod headers</code></pre>
 Creamos una archivo de configuración para las cabeceras de seguridad.

 ![Crear conf](../imagenes/A1C3N20.webp)

Habilitamos la nueva configuración

<pre><code>sudo a2enconf security_headers.conf</code></pre>

Testeamos la sintaxis y reiniciamos el servicio.

<pre><code>sudo apache2ctl configtest</code></pre>

<pre><code>sudo systemctl restart apache2</code></pre>

Con esta configuración, a partir de ahora, a cualquier sitio creado en el servidor se le aplicarán las cabeceras de seguridad ( excepto HSTS que están comentadas por que se activarán conado configuremos el tráfico HTTPS).

4. **Limitar el tamaño del cuerpo de la petición**
   Esto previene ataques de denegación de servicio (DoS) al evitar que un atacante envíe datos POST demasiado grandes que podrían agotar los recursos del servidor.
   Al igual que con las cabeceras de seguridad, podemos establecer esta limitación de manera global para todos nuestros Virtual Host.

   Creamos un archivo de configuración para 'LimitRequestBody':

   <pre><code>sudo nano /etc/apache2/conf-available/request_limits.conf</code></pre>

   Añadimos la directiva de limitación:

   ![Crear conf](../imagenes/A1C3N21.webp)

   Habilitamos esta nueva configuración:

   <pre><code>sudo a2enconf request_limits.conf</code></pre>

   Verificamos la sintaxis y reiniciamos el servicio:

   <pre><code>sudo apache2ctl configtest</code></pre>
   <pre><code>sudo systemctl restart apache2</code></pre>

   Con esto, cualquier petición que intente enviar un cuerpo de datos superior a 10MB será denegada por Apache, protegiendo nuestros recursos.

5. **Asegurar los directorios de log**
   Debemos verificar los permisos actuales de los archivos de log.
   
   Los archivos deben ser propiedad de www-data o root y el grupo adm con permisos de lectura -rw-r-----

   ![Crear conf](../imagenes/A1C3N22.webp)

   Si los permisos son diferentes o mas laxos deberían ajustarse.
   - 📌 Nota: adm es el grupo por defecto en Debian para los logs. En otras distribuciones podría ser root o syslog.
  
6. **Desactivar módulos innecesarios**
   Cuantos menos módulos estén activos menos seran las posibles vulnerabilidades que explotar.

   Listar los módulos actualmente habilitados:

   <pre><code>a2query -m</code></pre>

   Revisar cuidadosamente esta lista e identificar cualquier módulo cuya funcionalidad no sea estrictamente necesaria.

   En mi caso he seleccionado 3 módulos para eliminar:

   <pre><code>sudo a2dismod status</code></pre>

   <pre><code>sudo a2dismod access_compat</code></pre>

   <pre><code>sudo a2dismod autoindex -f</code></pre>

   Verificamos sintaxis y reiniciamos serviciio.

 7. **Monitorización de Logs de Apache**

La monitorización constante es una práctica indispensable para la depuración, análisis de tráfico y detección temprana de posibles problemas o actividades maliciosas.

- **Logs de acceso**
  <pre><code>sudo tail -f /var/log/apache2/access.log</code></pre>

- **Logs de error**
  
   <pre><code>sudo tail -f /var/log/apache2/error.log</code></pre>


## ✅ Cierre del capítulo

📌 **Resumen rápido**  
   - **Virtual Hosts:** Hemos configurado un sitio web completo (origen) usando Virtual Hosts, deshabilitando el sitio por defecto de Apache.
   - **Gestión de Módulos:** Aprendimos la importancia de habilitar (a2enmod) y configurar (a2enconf) módulos clave como deflate, expires, y headers de forma modular y global para optimizar el rendimiento y la seguridad.
   - **Seguridad Esencial:** Hemos implementado medidas de seguridad críticas, incluyendo:
     -  Ocultar la versión del servidor.
     -  Evitar el listado automático de directorios (Indexes).
     -  Establecer cabeceras HTTP de seguridad (X-Content-Type-Options, X-Frame-Options, X-XSS-Protection, Referrer-Policy).
     - Limitar el tamaño del cuerpo de las peticiones para prevenir ataques DoS.
     - Asegurar los permisos de los directorios de log.
     - Deshabilitar módulos innecesarios (status, access_compat, autoindex).
   - **Monitorización:** Comprendimos la importancia de los logs de acceso y error de Apache para depurar y mantener el servidor. 

🔧 **Estado actual del entorno**  
El servidor Apache2 en Debian 12 está ahora configurado con un Virtual Host funcional y modular, listo para servir tu blog. Hemos aplicado una serie de optimizaciones de rendimiento (compresión, caché de navegador) y, lo que es más importante, un conjunto robusto de medidas de seguridad a nivel de servidor, incluyendo la protección de acceso a directorios y la mitigación de ataques comunes. El entorno está más seguro, eficiente y preparado para el futuro.

🚀 **¿Y ahora qué?**  
El siguiente paso  es dar vida al blog. En el próximo capítulo explicaré como se está diseñado el contenido web estático.

💬 **Bitácora del viajero**  
> *""El servidor es el lienzo; ahora es el momento de empezar a pintar nuestra historia digital."*

