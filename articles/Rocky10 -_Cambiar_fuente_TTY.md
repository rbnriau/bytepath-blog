# Cambiar fuente de TTY en sistemas RHEL
**Fecha:** 2025-10-14  
**Tema:** 
Personalización de TTY

---

## Descripción rápida
  
Al instalar Rocky 10 sin entorno de escritorio, en largas sesiones de trabajo o estudio se hace agotador leer la TTY. Pueden cambiarse la fuente y el tamaño que vienen por defecto.

---

## Comandos usados

## Ruta a las fuentes de consola

En sistemas derivados de RHEL, la localización de las fuentes de la consola es diferente que en los sistemas basados en Debian.
Las podemos ver en formato comprimido en /usr/lib/kbd/consolefonts/

![listado de consolefonts](/img/rocky-fontconsole.webp)

Los archivos que usaré son los .psfu.gz ya que los psf.gz no interpretan muchos caracteres.
Podemos probarlas con este comando 
```bash
setfont solar24x32.psfu.gz
```
> No es necesario poner la ruta completa

Una vez que hemos decidido la fuente y su tamaño podemos asignarla por defecto en nuestro sistema editando el archivo /etc/vconsole.conf
![vconsole.conf](/img/vconsole.conf)

Tras guardar los cambios ejecutamos:
```bash
sudo dracut -f && sudo reboot
```
Cuando se reinicie el sistema ya estará asignada por defecto la fuente seleccionada




