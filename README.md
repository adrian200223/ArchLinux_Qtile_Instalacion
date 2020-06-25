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

loadkeys es

lsblk

cfdisk /dev/nvme0n1  (new, type linux filesystem y write)

mkfs.ext4 /dev/nvme0n1p7

cfdisk /dev/sda (new, type linux swap y write)

lsblk

mkswap /dev/sda1

swapon /dev/sda1

mount /dev/nvme0n1p7 /mnt

vim /etc/pacman.d/mirrorlist   (dejar sólo los servidores más cercanos geográficamente)

ping www.google.com  (si no va mira ip link show, ip link set enp3s0 up y comprobar conexión por cable, sino a la guía)

pacstrap /mnt base linux linux-firmware

------------------------------------------------------------------------------

genfstab -U /mnt >> /mnt/etc/fstab

arch-chroot /mnt

pacman -S networkmanager  (si no va a mirar ip link show y poner ip link set enp3s0 up)

systemctl enable NetworkManager

ln -sf /usr/share/zoneinfo/CET/Madrid /etc/localtime

hwclock --systohc

locale-gen

echo "LANG=es_ES.UTF-8" >> /etc/locale.conf

echo "KEYMAP=es" >> /etc/vconsole.conf

pacman -S neovim

nvim /etc/hostname (ahí escribo "localhost")

nvim /etc/hosts 
  (y escribo:
127.0.0.1	localhost
::1		localhost
127.0.1.1	localhost.localdomain localhost
)

passwd

pacman -S sudo

useradd -m <usuario>
  
passwd <usuario>
  
usermod -aG wheel,audio,video,optical,storage,kvm <usuario>
  
nvim /etc/sudoers o visudo  (hacen lo mismo, descomento %wheel ALL=(ALL) ALL)

(instalación del grub (pacman -S grub, grub-install /dev/nvme0n1, grub-mkconfig -o /boot/grub/grub.cfg), no necesaria en un equipo con otro linux, actualizo el grub con grub-customizer)

exit

shutdown +0

-------------------------------------------------------------------------------------------------------

ping www.google.com  (sino usa ip link, si "operation not permited" sudo ip link ...)

ip link show

sudo ip link set wlp2s0 up

nmcli device wifi list

nmcli device wifi connect <nombre del wifi> password <constraseña>  (en caso de nombre con espacios -> "wifi con espacios")
  
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

------------------------------------------------------------------------------------------------

setxkbmap es

sudo pacman -S git

sudo pacman -S alacritty

git clone https://github.com/antoniosarosi/dotfiles

cd dotfiles

sudo cp -R . /home/adrian -v

reboot

---------------------------------------------------------------------------------------------------

nvim ./.config/qtile/config.py

sudo pacman -S firefox

sudo pacman -S rofi

sudo nvim /etc/locale.gen (descomento en_US UTF-8 y es_ES UTF-8)

localectl

sudo locale-gen

sudo reboot

mkdir /usr/local/backgrounds

cp /home/<usuario>/Downloads/fondo_pantalla.png /usr/local/backgrounds
  
sudo pacman -S feh

nvim ~/.config/qtile/autostart.sh (añado feh --bg-scale /usr/local/backgrounds/fondo_pantalla.png y alacritty &)

sudo pacman -S cbatticon

sudo pacman -S neofetch

sudo nvim ~/.bashrc (comento líneas 11, 12 y 13, añado neofetch y modifico export PS1 buscando bashrc generator en firefox)

sudo pkill -KILL -u <usuario>
  
sudo nvim ~/.config/neofetch/config.conf

sudo pacman -S picom

sudo pacman -S brightnessctl

sudo pacman -S redshift

sudo rofi -show run -> lightdm-gtk-greeter-settings

# Comandos muy opcionales

sudo pacman -S dolphin

sudo pacman -S qt5ct

sudo pacman -S plasma   (otro entorno gráfico, por si qtile falla)

sudo pacman -S htop
