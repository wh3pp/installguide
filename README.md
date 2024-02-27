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
### 5. Montar las particiones
5.1 Montar /root (reemplazar **_sdXY_** con la etiqueta de la particion root)
```
mount /dev/sdXY /mnt
```
5.2 Crear directorios para montar la particion EFI
```
mkdir -p /mnt/boot/efi
```
5.3 Montar EFI (reemplazar **_sdXY_** con la etiqueta de la particion EFI)
```
mount /dev/sdXY /mnt/boot/efi
```
5.4 En caso de tenerla, activar la particion swap (reemplazar **_sdXY_** con la etiqueta de la particion swap)
```
swapon /dev/sdXY
```
### 6. Instalar paquetes iniciales
Paquetes base:
```
pacstrap /mnt base base-devel linux linux-firmware nano grub git networkmanager wpa_supplicant
```
Paquetes adicionales (en caso de tener instalado el sistema operativo Windows):
```
pacstrap /mnt efibootmgr os-prober
```
### 7. Generar el archivo fstab
> [!NOTE]
> El archivo /etc/fstab (abreviatura de "file system table") es un archivo de configuración en sistemas operativos Unix y Linux que mapea las particiones de disco y dispositivos de almacenamiento en el sistema de archivos. En él, se especifican las opciones de montaje y configuraciones asociadas para cada partición o dispositivo.
```
genfstan -U /mnt >> /mnt/etc/fstab
```
### 8. Cambiar al entorno chroot
> [!NOTE]
> El entorno chroot es una herramienta que permite crear un ambiente aislado dentro del sistema operativo donde un proceso puede ejecutarse con un sistema de archivos diferente al del sistema principal. Esto es útil para realizar tareas de mantenimiento, instalación de sistemas operativos o ejecutar aplicaciones que necesitan acceder a un conjunto específico de archivos y recursos.
```
arch-chroot /mnt
```
### 9. Agregar contraseña y usuario
9.1 Contraseña a /root
```
passwd
```
9.2 Agregar usuario y asignarle una contraseña (cambiar **_usuario_** por el nombre deseado)
```
useardd -m usuario
```
```
passwd usuario
```
9.3 Agregar privilegios root a usuario (cambiar **_usuario_** por el nombre deseado)
```
usermod -aG wheel usuario
```
9.4 Configuración de permisos de sudo
```
nano /etc/sudoers
```
Buscar (ctrl + w) esta parte:
```
## Uncomment to allow members of group wheel to execute any command
#%wheel ALL=(ALL:ALL) ALL
```
9.5 Agregar nombre a tu portatil o pc (Cambiar **nombre** por el nombre de la pc deseada, en caso de que no exista el archivo /etc/hostname crearlo con `mkdir /etc/hostname`)
```
echo nombre > /etc/hostname
```
Descomentar eliminando el **#** que precede a **wheel**, presionar **ctrl + o**, confirmar y presionar **ctrl + x** para salir.
### 10. Configuraciones adicionales
10.1 Zona horaria
```
ln -sf /usr/share/zoneinfo/Región/Ciudad /etc/localtime
```
Por ejemplo para Perú:
```
ln -sf /usr/share/zoneinfo/America/Lima /etc/localtime
```
Adicionalmente:
```
hwclock --systohc
```
10.2 Idioma del sistema
```
nano /etc/locale.gen
```
Buscar (Ctrl + w) tu idioma y decomentar la que este en `UTF-8`, por ejemplo para USA: `en_US`, Peru: `es_PE`
> [!NOTE]
> Se puede descomentar múltiples idiomas

Generar
```
locale-gen
```
10.2 Distribución de teclado

Para el latinoamericano:
```
echo "KEYMAP=la-latin1\nXKBLAYOUT=latam" > /etc/vconsole.conf
```
10.3 Instalar grub (Si se desea cambiar Arch por otro
```
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=Arch
```
10.4 Generar el archivo de configuracion de grub
```
grub-mkconfig -o /boot/grub/grub.cfg
```
10.5 Permitir la ejecución de os-prober para detectar otros sistemas operativos para grub
```
echo "GRUB_DISABLE_OS_PROBER=false" >> /etc/default/grub
```
10.6 
