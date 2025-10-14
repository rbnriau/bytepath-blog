# Cambiar fuente de TTY en sistemas RHEL
**Fecha:** 2025-10-14  
**Tema:** 
Personalización de TTY

---

## Descripción rápida
  
Al instalar Rocky 10 sin entorno de escritorio, en largas sesiones de trabajo o estudio se hace agotador leer la TTY. Pueden cambiarse la fuente y el tamaño que vienen por defecto.

---

## Ruta a las consolas

En sistemas derivados de RHEL, la localización de las fuentes de la consola es diferente que en los sistemas basados en Debian.
Las podemos ver en formato commprimido en /usr/lib/kbd/consolefonts/

## ⚙️ Comandos usados
```bash
# ejemplo
setfont Lat15-TerminusBold20x10
