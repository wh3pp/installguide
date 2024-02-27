# Arch linux guia
## Preinstalación
### 1. Descargar el iso desde la [página oficial](https://archlinux.org/download/).
> [!NOTE]
> Se recomienda descargarlo mediante el archivo torrent con [qbitorrent](https://www.fosshub.com/qBittorrent.html).
### 2. Preparar el medio de instalación (USB) mediante [rufus](https://rufus.ie/en/).
> [!NOTE]
> Se recomienda el esquema de particion GPT y lo demás por defecto.
### 3. Iniciar el live usb desde la BIOS o UEFI
> [!IMPORTANT]
> * Acer: F2
> * ASUS: F2 o Suprimir
> * Compaq: F10
> * Dell: F2
> * Fujitsu: F2
> * HP: Escape o F10
> * Lenovo: F1, F2 o botón nano
> * Packard Bell: F8
> * Samsung: F10 o F2
> * Sony: F1, F2, F3 o botón de asistencia
> * Toshiba: Escape, F2 o F1
## Instalación
### 1. Cambiar a teclado latino
```
loadkeys la-latin1
```
### 2. Conectarse a internet mediante iwctl
> [!NOTE]
> Comprobar que la tarjeta no esté bloqueada con rfkill.
> ```
> rfkill
> ```
> Si la salida muestra que la tarjeta de wifi se encuentra unblocked proseguir
> ```
> ID TYPE      DEVICE      SOFT      HARD
> 0 bluetooth hci0   unblocked unblocked
> 1 wlan      phy0   unblocked unblocked
> ```
> Caso contrario desbloquearla con:
> ```
> rfkill unblock wlan
> ```
Abrir iwctl
```
iwctl
```
lista todos los dispositivos Wi-Fi
```
device list
```
En caso de que el dispositivo o adaptador se encuentre apagdo encenderlo

Para un dispositivo (reemplazar dispositivo con el nombre del dispositivo):
```
device dispositivo set-property Powered on
```
Para un adaptador (reemplazar adaptador con el nombre del adaptador):
```
adapter adaptador set-property Powered on
```
Escanear las redes wifi disponibles:
```
station dispositivo scan
```
Listar las redes wifi disponibles:
```
station dispositivo get-networks
```
Conectarse a la red wifi requerida (reemplazar SSID con el nombre de la red wifi):
```
station dispositivo connect SSID
```
Ingresar la contraseña, salir y comprobar la conección a internet:
```
exit
```
```
ping -c 2 google.com
```
### 3. Particionar el disco mediante cfdisk
> [!NOTE]
> En caso se tengan las particiones y solo se quiera reinstalar Arch en el mismo disco, saltar al paso 4.

> [!IMPORTANT]
> Los discos se asignan a un dispositivo de bloque como /dev/sda, /dev/nvme0n1 o /dev/mmcblk0. Para identificar estos dispositivos, utilice lsblk o fdisk:
> ```
> fdisk -l
> ```
> Los resultados que terminan en rom, loop o airoot pueden ignorarse.
> Tener identificado dichas etiquetas

Ejecutar cfdisk:
```
cfdisk
```
Las siguientes particiones son necesarias para el dispositivo elegido:

* Una partición para el directorio raíz /.
* Para arrancar en modo UEFI: una partición del sistema EFI (al menos 300 MiB).
> [!NOTE]
> En caso de haber instalado el sistema operativo Windows con anterioridad se recomienda no agregar otra particion del sistema EFi.

En caso se requiera agregar una particion swap (Memoria RAM de respaldo, mas de 512 MiB)

Al finalizar darle a la opción de **_Write_**, y escribir **_yes_**
### 4. Formatear las particiones
4.1 Formatear la particion EFI (reemplazar **_sdXY_** con la etiqueta de la particion efi)
> [!NOTE]
> En caso tener instalado el sistema operativo Windows con anterioridad no formatear la particion EFI y pasar al paso 4.2
```
mkfs.vfat -F32 /dev/sdXY
```
4.2 Formatear la particion root (reemplazar **_sdXY_** con la etiqueta de la particion root)
```
mkfs.ext4 /dev/sdXY
```
4.3 Formatear la particion swap (reemplazar **_sdXY_** con la etiqueta de la particion swap)
```
mkfswap /dev/sdXY
```
### 5. Activar la particion swap (En caso de tenerla)
reemplazar **_sdXY_** con la etiqueta de la particion swap
```
mkfswap /dev/sdXY
```
### 6. Montar las particiones en /mnt
6.1 Montar /root (reemplazar **_sdXY_** con la etiqueta de la particion root)
```
mount /dev/sdXY /mnt
```
6.2 Crear directorios para montar la particion EFI
```
mkdir -p /mnt/boot/efi
```
6.3 Montar EFI (reemplazar **_sdXY_** con la etiqueta de la particion EFI)
```
mount /dev/sdXY /mnt/boot/efi
```
