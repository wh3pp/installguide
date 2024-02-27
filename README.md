# Arch linux guia
## Preinstalación
### 1. Descargar el iso desde la [página oficial](https://archlinux.org/download/).
> [!NOTE]
> Se recomienda descargarlo mediante archivo torrent con [qbitorrent](https://www.fosshub.com/qBittorrent.html).
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
```
iwctl
```
lista todos los dispositivos Wi-Fi
```
device list
```
En caso de que el dispositivo o adaptador se encuentre apagdo encenderlo, reemplazar _device_ con el nombre del dispositivo.

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
Conectarse a la red wifi requerida (reemplazar SSID con el nombre de la red wifi)
```
station dispositivo connect SSID
```
Ingresar la contraseña, salir y comprobar la conección a internet
```
exit
```
```
ping -c 2 google.com
```
### 3. Particionar el disco mediante cfdisk
> [!NOTE]
> Los discos se asignan a un dispositivo de bloque como /dev/sda, /dev/nvme0n1 o /dev/mmcblk0. Para identificar estos dispositivos, utilice lsblk o fdisk:
> ```
> fdisk -l
> ```

# fdisk -l
```
cfdisk
```
### 3. Montar el disco rooten /mnt
```
mount /dev/sd** /mnt
```
