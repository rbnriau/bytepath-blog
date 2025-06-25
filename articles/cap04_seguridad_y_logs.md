# 🧯 Capítulo 07 – Seguridad y Logs

Este capítulo es fundamental para la administración de cualquier servidor. Un buen conocimiento y práctica en la revisión de logs es la primera línea de defensa para la detección temprana de problemas, ataques o irregularidades. No solo nos ayuda a depurar, sino que también nos proporciona información vital sobre el comportamiento de nuestro sistema y aplicaciones.

## 📦 Logwatch

Logwatch es una herramienta muy útil para la monitorización automatizada y resumida de los logs del sistema. En lugar de tener que revisar manualmente cada archivo de log (Apache, SSH, sistema, etc.), Logwatch los recopila, analiza y nos envía un resumen diario (o con la frecuencia que configuremos) por correo electrónico. Esto nos permite tener una visión rápida y eficiente de lo que está sucediendo en nuestro servidor.

**Porqué usar Logwatch?**
- **Eficiencia:** Recibe un resumen conciso en lugar de leer logs voluminosos.
- **Detección Temprana:** Ayuda a identificar patrones inusuales o actividades sospechosas que podrían indicar un problema de seguridad o un fallo del sistema.
- **Centralización:** Agrupa información de múltiples logs (Apache, SSH, kernel, etc.) en un solo informe.

El envío de correos al administrador por parte de Logwatch implica tener instalado SSL/TLS. Como ya decidí hacer este paso una vez esté en la nube, pospongo la instalación y configuración de Logwatch para más adelante.

## 🔎 journalctl y dmesg

Estas dos herramientas son fundamentales para la inspección de logs del sistema a un nivel más bajo, específicamente relacionados con systemd (el gestor de sistemas y servicios en Debian) y el kernel de Linux. Son vitales para diagnosticar problemas del sistema o de bajo nivel que no necesariamente aparecen en los logs de aplicaciones.

### journalctl
 permite consultar y gestionar los logs que systemd recoge. Estos logs son binarios y se almacenan en el journal, lo que los hace más eficientes y permite filtrar de maneras muy potentes.

**Comandos útiles de journalctl:**


1. Ver todos los logs del sistema.
- <pre><code>journalctl</code></pre>
2. Ver los logs en tiempo real.
- <pre><code>journalctl -f</code></pre>
3.- Ver logs de un servicio específico (ej. Apache):
- <pre><code>journalctl -u apache2.service</pre></code>
- <pre><code>journalctl -u apache2.service -f # En tiempo real</code></pre>
4. Filtrar por tiempo:
- <pre><code>journalctl --since "2025-06-01 10:00:00" --until "2025-06-01 10:30:00"</code></pre>
- <pre><code>journalctl --since "yesterday"</code></pre>
- <pre><code>journalctl --since "1 hour ago"</code></pre>
5. Ver mensajes de error y advertencias.
- <pre><code>journalctl -p err</code></pre>
- <pre><code>journalctl -p warning</code></pre>
### dmesg (diagnose message)
 Es una herramienta para imprimir o controlar el buffer de mensajes del kernel. Estos mensajes se generan durante el arranque del sistema y por los controladores de hardware mientras el sistema está en funcionamiento. Son cruciales para diagnosticar problemas de hardware, controladores, memoria o errores de bajo nivel del sistema.

**Comandos útiles de dmesg**
1. Ver todos los mensajes del kernel
-  <pre><code>dmesg</code></pre>
2. Ver los últimos mensajes del kernel
- <pre><code>dmesg | tail</code></pre>
3. Filtrar mensajes del kernel
- <pre><code>dmesg | grep -i error</code></pre>
- <pre><code>dmesg | grep -i "fail"</code></pre>
- <pre><code>dmesg | grep -i "memory"</code></pre>
- 📌 La opción -i hace que la búsqueda no distinga entre mayúsculas y minúsculas.
4. Borrar el buffer de dmesg (requiere sudo):
- <pre><code>sudo dmesg -c</code></pre>

## 📝 Revisión básica de logs

Esta sección es más conceptual y de "buenas prácticas". Aunque hemos visto herramientas específicas, es importante saber dónde buscar y qué buscar.
### Directorios de logs comunes
1. <pre><code>/var/log/syslog</code></pre>
- Logs generales del sistema.
2. <pre><code>/var/log/auth.log</code></pre>
- Logs relacionados con la autenticación.
3. <pre><code>/var/log/apache2/</code></pre>
- Contiene los logs de Apache
4. <pre><code>/var/log/apt/history.log</code></pre>
- Registra las instalaciones, actualizaciones y eliminaciones de paquetes APT. Útil para saber qué se ha cambiado en el sistema.
5. <pre><code>/var/log/faillog</code></pre>
-  Registra los intentos fallidos de inicio de sesión.
6. <pre><code>/var/log/mail.log</code></pre>

### Monitoreo Básico de Recursos 
**Comandos de monitoreo básico de CPU, RAM y disco**
- Uso de CPU y RAM:
  - <pre><code>htop y top</code></pre>
- Uso de RAM:
  - <pre><code>free -h</code></pre>
- Uso de espacio en disco:
  - <pre><code>df -h</code></pre>
  
### Consejos para la revisión de logs

- **Revisión Regular:** Habituarse a revisar los logs de forma regular, al menos una vez al día para un servidor en producción.
- **Patrones de Comportamiento:** Aprender a reconocer el "ruido" normal de los logs y a identificar patrones inusuales.
- **Errores Recientes:** Después de hacer cambios en la configuración o implementar nuevas características, revisar el error.log de Apache y journalctl -u mi_servicio para detectar cualquier problema inmediatamente.
- **Correlación de Logs:** Si se detecta un problema en un log (ej. un error de Apache), intentar correlacionarlo con otros logs (ej. syslog o auth.log) para obtener una imagen completa de lo que sucedió.
- **Automatización:** Además de Logwatch, considerar usar herramientas más avanzadas en el futuro como ELK Stack (Elasticsearch, Logstash, Kibana) o Grafana Loki para centralización y visualización de logs a gran escala.

## ✅ Cierre del capítulo

📌 **Resumen rápido**  
  - **Logwatch:** Introdujimos Logwatch como una herramienta para la monitorización automatizada y resumida de logs, entendiendo su propósito para la detección temprana de problemas y seguridad, posponiendo su configuración para la nube.
  - **Herramientas de Logs de Sistema:** Nos familiarizamos con journalctl para la consulta de logs de systemd y dmesg para los mensajes del kernel, esenciales para el diagnóstico a bajo nivel.
  - **Revisión Básica de Logs:** Cubrimos las ubicaciones clave de los logs en /var/log/ y practicamos herramientas esenciales de línea de comandos como tail, less y grep para la inspección directa de archivos.
  - 

🔧 **Estado actual del entorno**  
Nuestro servidor Apache2 en Debian 12 ahora no solo está configurado para servir nuestro blog de forma segura y eficiente, sino que también tenemos las herramientas y el conocimiento básico para monitorear su salud y actividad. Podemos revisar logs de sistema, detectar posibles anomalías y observar el consumo de recursos directamente desde la línea de comandos.

🚀 **¿Y ahora qué?**  
Con una comprensión sólida del monitoreo básico, ¡es hora de dar el gran salto! En el próximo capítulo, nos centraremos en la migración de nuestro blog a Google Cloud Platform (GCP). Una vez allí, configuraremos el crucial HTTPS con Let's Encrypt, instalaremos y activaremos Logwatch para recibir resúmenes diarios por correo, y finalmente, daremos forma a la portada de nuestro sitio web y los capítulos que documentan toda esta aventura.

💬 **Bitácora del viajero**  
> *"Los logs son la voz de tu servidor; aprender a escucharlos es el primer paso para dominar su funcionamiento y asegurar su silencio ante las amenazas."*
