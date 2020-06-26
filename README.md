# ArchLinux, Instalacion con Qtile
Compilación de comandos para instalar el arch. 

# Aviso

No usarlos sin modificarlos antes para tu caso. Especialmente los comandos con elementos como /dev/nvme0n1 y /dev/sda. 

No estoy haciendo los pasos de verificar el modo de boot (BIOS o UEFI) ni la verificación de firma en el iso de instalación.

# Fuentes

https://wiki.archlinux.org/index.php/installation_guide

https://www.youtube.com/channel/UCzTi9I3zApECTkukkMOpEEA

https://www.youtube.com/channel/UCVls1GmFKf6WlTraIb_IaJg

https://www.youtube.com/channel/UCJdmdUp5BrsWsYVQUylCMLg


## Preparación:

Rufus: https://rufus.ie/

Arch-Linux: https://www.archlinux.org/download/

1. Con rufus configura un pen-drive con el instalador de arch linux.
2. Desde la BIOS accede al pen drive en el boot menu.
3. Recomendado conectar un cable ethernet al ordenador.

## Instalación:
```bash
loadkeys es

lsblk
```
```bash
cfdisk /dev/nvme0n1
```
En cfdisk new, type linux filesystem y write
```bash
mkfs.ext4 /dev/nvme0n1p7
```
```bash
cfdisk /dev/sda 
```
En cfdisk, de nuevo, new, type linux swap y write
```bash
lsblk

mkswap /dev/sda1

swapon /dev/sda1

mount /dev/nvme0n1p7 /mnt
```
```bash
vim /etc/pacman.d/mirrorlist   
```
Dejo sólo los servidores más cercanos geográficamente
```bash
ping www.google.com 
```
Si no va mira `ip link show`, `ip link set enp3s0 up` y comprobar conexión por cable, sino hay que ir a la guía de instalación de arch.

```bash
pacstrap /mnt base linux linux-firmware
```
------------------------------------------------------------------------------
```bash
genfstab -U /mnt >> /mnt/etc/fstab

arch-chroot /mnt
```
```bash
pacman -S networkmanager 
```
Si la descarga no va, mira `ip link show` y pon luego `ip link set enp3s0 up`

```bash
systemctl enable NetworkManager

ln -sf /usr/share/zoneinfo/CET/Madrid /etc/localtime

hwclock --systohc

locale-gen

echo "LANG=es_ES.UTF-8" >> /etc/locale.conf

echo "KEYMAP=es" >> /etc/vconsole.conf

pacman -S neovim
```
```bash
nvim /etc/hostname
```
En ese archivo escribo: `localhost` y guardo el documento.
```bash
nvim /etc/hosts
```
En ese archivo escribo:
```bash
127.0.0.1	localhost
::1		    localhost
127.0.1.1	localhost.localdomain localhost
```
Y guardo el documento.
```bash
passwd

pacman -S sudo

useradd -m <usuario>
  
passwd <usuario>
  
usermod -aG wheel,audio,video,optical,storage,kvm <usuario>
``` 
A continuación:
`nvim /etc/sudoers` o `visudo`  (hacen lo mismo) y descomento la línea con `%wheel ALL=(ALL) ALL` (quitando el #).

Es necesario realizar la instalación del grub u otro bootloader (creo que sería con `pacman -S grub`, `grub-install /dev/nvme0n1`, `grub-mkconfig -o /boot/grub/grub.cfg`). En mi caso actualizo desde ubuntu el grub con grub customizer y así aparece en las opciones.

```bash
exit

shutdown +0
```
-------------------------------------------------------------------------------------------------------
```bash
ping www.google.com
```
Si no va usa ip link, como en los casos anteriores. "Operation not permitted" significa que por ejempolo `ip link set enp3s0 up` pasa a ser `sudo ip link set enp3s0 up` (y pide la contraseña).

```bash
ip link show

sudo ip link set wlp2s0 up

nmcli device wifi list
```
```bash
nmcli device wifi connect <nombre del wifi> password <constraseña>  
```
En caso de nombre con espacios -> "wifi con espacios"
```bash
ping www.google.com

sudo pacman -Syu

sudo pacman -S xorg

sudo pacman -S xorg-server xorg-apps xorg-xinit xterm

sudo pacman -S lightdm-gtk-greeter lightdm-gtk-greeter-settings

sudo systemctl enable lightdm.service

mkdir -p ~/.config/qtile

sudo pacman -S qtile

cp /usr/share/doc/qtile/default_config.py ~/.config/qtile/config.py

reboot
```
------------------------------------------------------------------------------------------------
```bash
setxkbmap es

sudo pacman -S git

sudo pacman -S alacritty

git clone https://github.com/antoniosarosi/dotfiles

cd dotfiles

sudo cp -R . /home/adrian -v

reboot
```
---------------------------------------------------------------------------------------------------
```bash
nvim ./.config/qtile/config.py

sudo pacman -S firefox

sudo pacman -S rofi
```
```bash
sudo nvim /etc/locale.gen 
```
Descomento `en_US UTF-8` y `es_ES UTF-8` en locale.gen

```bash
localectl

sudo locale-gen

sudo reboot

mkdir /usr/local/backgrounds

cp /home/<usuario>/Downloads/fondo_pantalla.png /usr/local/backgrounds
  
sudo pacman -S feh
```
```bash
nvim ~/.config/qtile/autostart.sh 
```
Añado `feh --bg-scale /usr/local/backgrounds/fondo_pantalla.png` y `alacritty &`

```bash
sudo pacman -S cbatticon

sudo pacman -S neofetch
```
```bash
sudo nvim ~/.bashrc
```
Comento líneas 11, 12 y 13, añado `neofetch` y modifico export PS1 usando http://ezprompt.net/

```bash

sudo pkill -KILL -u <usuario>
  
sudo nvim ~/.config/neofetch/config.conf

sudo pacman -S picom

sudo pacman -S brightnessctl

sudo pacman -S redshift

sudo rofi -show run -> lightdm-gtk-greeter-settings
```

# Comandos muy opcionales

```bash
sudo pacman -S dolphin

sudo pacman -S qt5ct

sudo pacman -S plasma   
```
Plasma es otro entorno gráfico, por si qtile falla, pero no es para nada necesario.

```bash
sudo pacman -S htop
```
