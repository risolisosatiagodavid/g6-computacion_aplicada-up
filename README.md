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

---

## Diagrama Topológico

```text
+===================================================================================================================+
|                                           HOST FÍSICO (Windows / Anfitrión)                                       |
|                                                                                                                   |
|   +-----------------------------------------------------------------------------------------------------------+   |
|   |  HIPERVISOR: Oracle VirtualBox                                                                            |   |
|   |                                                                                                           |   |
|   |   +---------------------------------------------------------------------------------------------------+   |   |
|   |   |  MÁQUINA VIRTUAL: TPServer (Debian GNU/Linux 11/12)                                               |   |   |
|   |   |  * Hostname: TPServer                                                                             |   |   |
|   |   |  * Credenciales de administración: root / palermo                                                 |   |   |
|   |   |                                                                                                   |   |   |
|   |   |  [ CAPA DE RED / INTERFACES ]                                                                     |   |   |
|   |   |  +---------------------------------------------------------------------------------------------+  |   |   |
|   |   |  | Adaptador 1 (Bridged / Puente): enp0s3                                                      |  |   |   |
|   |   |  |   - IP Estática: 10.128.2.204 / Máscara: 255.255.0.0 (/16)                                  |  |   |   |
|   |   |  |   - Gateway: 10.128.0.1 (Configuración persistente en /etc/network/interfaces)              |  |   |   |   
|   |   |  +---------------------------------------------------------------------------------------------+  |   |   |
|   |   |                                                                                                   |   |   |
|   |   |  [ SERVICIOS DEL SISTEMA Y PUERTOS ]                                                              |   |   |
|   |   |  +-------------------------+-------------------------------------------------------------------+  |   |   |
|   |   |  | OpenSSH Server          | Puerto 22/TCP                                                     |  |   |   |
|   |   |  |                         | Auth: Par de llaves pública/privada (root permit login)           |  |   |   |
|   |   |  |                         | Acceso remoto: VS Code (Remote-SSH via 127.0.0.1:2222)            |  |   |   |
|   |   |  +-------------------------+-------------------------------------------------------------------+  |   |   |
|   |   |  | Servidor Web Apache 2   | Puerto 80/TCP                                                     |  |   |   |
|   |   |  |                         | Módulos: MPM Prefork, PHP 7.3+, php-mysql                         |  |   |   |
|   |   |  |                         | VirtualHost: /etc/apache2/sites-available/000-default.conf        |  |   |   |
|   |   |  |                         | DocumentRoot: /www_dir (Permisos www-data:www-data)               |  |   |   |
|   |   |  +-------------------------+-------------------------------------------------------------------+  |   |   |
|   |   |  | MariaDB Server          | Puerto 3306/TCP                                                   |  |   |   |
|   |   |  |                         | Servicio: mariadb.service (Socket unix / localhost)               |  |   |   |
|   |   |  |                         | Base de datos cargada: db.sql                                     |  |   |   |
|   |   |  +-------------------------+-------------------------------------------------------------------+  |   |   |
|   |   |  | Cron Daemon & Scripts   | /etc/crontab (Usuario root)                                       |  |   |   |
|   |   |  |                         | Script ejecutable: /opt/scripts/backup_full.sh                    |  |   |   |
|   |   |  |                         | - 00:00 hs (Diario): /var/log  -> /backup_dir/log_bkp_YYYYMMDD    |  |   |   |
|   |   |  |                         | - 23:00 hs (L, M, V): /www_dir -> /backup_dir/www_bkp_YYYYMMDD    |  |   |   |
|   |   |  +-------------------------+-------------------------------------------------------------------+  |   |   |
|   |   |                                                                                                   |   |   |
|   |   |  [ TOPOLOGÍA DE ALMACENAMIENTO Y MONTAJE (/etc/fstab) ]                                           |   |   |
|   |   |                                                                                                   |   |   |
|   |   |  Disco 1: /dev/sda (Disco Primario del Sistema)                                                   |   |   |
|   |   |    |-- /dev/sda1 (ext4)  ------> Montado en: / (Root FileSystem)                                  |   |   |
|   |   |    `-- /dev/sda5 (swap)  ------> Área de intercambio (SWAP)                                       |   |   |
|   |   |                                                                                                   |   |   |
|   |   |  Disco 2: /dev/sdb (Almacenamiento de Usuarios)                                                   |   |   |
|   |   |    `-- /dev/sdb1 (ext4)  ------> Montado en: /home                                                |   |   |
|   |   |                                                                                                   |   |   |
|   |   |  Disco 3: /dev/sdc (Disco Secundario dedicado - 10 GB)                                            |   |   |
|   |   |    |-- /dev/sdc1 (ext4 - 3 GB, Id 83)                                                             |   |   |
|   |   |    |     |                                                                                        |   |   |
|   |   |    |     `---> Montado en: /www_dir                                                               |   |   |
|   |   |    |           Contenido: index.php (Web PHP conectada a DB), logo.png                            |   |   |
|   |   |    |                                                                                              |   |   |
|   |   |    `-- /dev/sdc2 (ext4 - 6 GB, Id 83)                                                             |   |   |
|   |   |          |                                                                                        |   |   |
|   |   |          `---> Montado en: /backup_dir                                                            |   |   |
|   |   |                Contenido: Repositorio de backups generados en formato ANSI                        |   |   |
|   |   |                                                                                                   |   |   |
|   |   +---------------------------------------------------------------------------------------------------+   |   |
|   +-----------------------------------------------------------------------------------------------------------+   |
+===================================================================================================================+
