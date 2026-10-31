# Trabajo Práctico Integrador - Computación Aplicada (GNU/Linux)

Este repositorio contiene los entregables, configuraciones y evidencias del Trabajo Práctico Integrador de la cátedra de Computación Aplicada (Universidad de Palermo), dictada por el profesor Pablo Ferro

---

## Integrantes del Grupo
* **Tiago David Risoli Sosa** 
* **Yeraldy Vilcapuma Roncal** 

---

## Resumen de la Infraestructura Configurada

### 1. Entorno Base y Hostname
* **Sistema Operativo:** GNU/Linux Debian.
* **Hostname asignado:** `TPServer`.
* **Gestión de credenciales:** Clave administrativa de `root` reestablecida a `palermo`.

### 2. Configuración de Red
* **Interfaz de red:** Configurada de forma persistente con direccionamiento IP estático.
* **Parámetros definidos:** Inclusión de directivas `ADDRESS`, `NETMASK` y `GATEWAY` en el mismo segmento de red del host anfitrión.

### 3. Servicios Implementados
* **SSH (Puerto 22):** Servicio `openssh-server` configurado para autenticación segura de `root` mediante par de llaves criptográficas pública/privada provistas por la cátedra.
* **Servidor Web Apache + PHP:** Servidor HTTP Apache 2 con soporte PHP (versión 7.3+) configurado para servir los archivos dinámicos `index.php` y estáticos `logo.png`.
* **Base de Datos MariaDB (Puerto 3306):** Motor relacional MariaDB instalado y cargado con el script relacional `db.sql`.

### 4. Esquema de Almacenamiento y Particionado
Se incorporó un disco rígido virtual secundario de **10 GB** con dos particiones estándar (Id 83 - Linux nativo) montadas automáticamente en arranque mediante `/etc/fstab`:
* **`/www_dir` (3 GB):** Directorio raíz web del servicio Apache (`DocumentRoot`) que contiene `index.php` y `logo.png`.
* **`/backup_dir` (6 GB):** Partición destinada al repositorio de respaldos automatizados.
* **Nota `/proc/particion`:** Redirección del contenido de `/proc/partitions` al nodo indicado.

### 5. Automatización y Backups
* **Script:** `/opt/scripts/backup_full.sh`.
* **Funcionalidades:**
  * Admite argumentos de origen y destino.
  * Parámetro `-help` con documentación de uso interactiva.
  * Validación previa de disponibilidad de los sistemas de archivos de origen y destino antes del proceso de empaquetado.
  * Nomenclatura de compresión en formato ANSI (`YYYMMDD` / `YYYYMMDD`, ej: `log_bkp_YYYYMMDD.tar.gz`).
* **Calendarización (`cron`):**
  * Respaldo diario de `/var/log` a las `00:00 hs` hacia `/backup_dir`.
  * Respaldo de `/www_dir` los días lunes, miércoles y viernes a las `23:00 hs` hacia `/backup_dir`.

---

## Estructura de Entregables (Archivos Comprimidos)

Siguiendo las pautas de entrega, se generaron y empaquetaron individualmente los directorios solicitados:

* `root.tar.gz` (o `root.tar.gz.part_*`) - Contenido íntegro del directorio administrativo `/root` (fragmentado en partes para respetar límites de GitHub).
* `etc.tar.gz` - Configuraciones de servicios (`apache2`, `network/interfaces`, `fstab`, `crontab`, etc.).
* `opt.tar.gz` - Scripts de automatización en `/opt/scripts`.
* `proc.tar.gz` - Estructura requerida incluyendo el archivo de particiones.
* `www_dir.tar.gz` - Código del sitio web y dependencias alojadas en el disco de 3 GB.
* `backup_dir.tar.gz` - Respaldo del almacenamiento de 6 GB.
* `var.tar.gz.part_*` - Directorio `/var` empaquetado y fragmentado en volúmenes pequeños mediante `split` para superar las restricciones de subida de GitHub.
