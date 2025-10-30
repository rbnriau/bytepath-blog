# Uso de pv para comprimir

**Fecha:** 20-10-2025

---

## Descripción general

Cuando se comprime un archivo muy grande es útil usar pv que nos muestra un seguimiento del proceso.

---

## Comandos utilizados

```bash
sudo apt update
sudo apt install pv 
# Puede ser que ya esté instalado en el sistema
```

Al usar este comando junto con tar y la compresión la sintaxis cambia, ya no debemos poner la opción -f habitual porqeu vamos a redireccionar a pv de esta manera.

```
tar -cJ [fichero_origen] | pv > [fichero_destino.tar.xz]
```

Nos aparecerá el volumen comprimido, velocidad de compresión y una marca que se mueve que nos indica que no se ha quedado 'colgado' el proceso.

Algo así:

```
3,91GiB 0:30:03 [2,46 MiB/s] [   <=>                 ]   
```