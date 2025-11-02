# Actualización de Nextcloud en VPS Debian 12
## 1. Copias de seguridad
Antes de cualquier cambio, realizamos backups completos:
- Archivos de Nextcloud:
```
sudo tar -czvf nextcloud-files-backup.tar.gz /var/www/nextcloud
```
- Base de datos (MariaDB/MySQL):
```
mysqldump -u nextcloud_user -p nextcloud > nextcloud-db-backup.sql
```
- Archivos de configuración importantes:
```
sudo cp /var/www/nextcloud/config/config.php ~/backup_config.php
```
Advertencia: Siempre verificamos que los permisos y propietarios sean correctos antes de cualquier restauración.

## 2. Activar modo mantenimiento
```
sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --on
```
## 3. Descargar nueva versión
- Descargamos la versión ZIP desde el sitio oficial de Nextcloud a nuestro PC local.
- Subimos el ZIP al servidor vía scp o SFTP.
- Descomprimimos en un directorio temporal:

## 4. Preparación de la actualización manual
- Movemos la instalación actual a -old:
```
sudo mv /var/www/nextcloud /var/www/nextcloud_old
```
- Movemos la nueva versión a /var/www/nextcloud:
```
sudo mv /var/www/nextcloud-27.x.x /var/www/nextcloud
```
- Copiamos la carpeta config y data de la versión anterior a la nueva:
```
sudo cp -r /var/www/nextcloud_old/config /var/www/nextcloud/
sudo cp -r /var/www/nextcloud_old/data /var/www/nextcloud/
```
- Ajustamos permisos:
```
sudo chown -R www-data:www-data /var/www/nextcloud
sudo find /var/www/nextcloud/ -type d -exec chmod 750 {} \;
sudo find /var/www/nextcloud/ -type f -exec chmod 640 {} \;
```
## 5. Actualización de la base de datos y apps
- Accedemos al actualizador desde el dashboard o usamos:
```
sudo -u www-data php /var/www/nextcloud/updater/updater.phar
```
- Durante la actualización, Nextcloud crea versiones -old_* de las carpetas de apps si es necesario.
- Se verifican y actualizan todas las apps instaladas.
- Eventuales errores menores se resolvieron revisando permisos o moviendo manualmente archivos faltantes.
## 6. Finalización
- Desactivamos modo mantenimiento( si se ha actualizado desde el dashboard, el instalador lo desactivará automáticaente)
```
sudo -u www-data php /var/www/nextcloud/occ maintenance:mode --off
```
- Verificamos acceso al dashboard y que todas las apps funcionen correctamente.
- Revisión final de logs:
```
sudo tail -f /var/www/nextcloud/updater-*/updater.log
sudo tail -f /var/www/nextcloud/nextcloud.log
```
Advertencia: Siempre conservar las carpetas *-old  y .bakups hasta verificar que todo funciona, luego pueden eliminarse.


