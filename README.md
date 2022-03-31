# Archlinux_installation


## Tastaturlayout einstellen
```sh
loadkeys de-latin1
Font
Setfont ter-132n
```

```sh
Localectl | grep DE
Pacman -Syy
```

# Netzwerk- und Internetzugang
```sh
Ip a

Ip route falls keine Internet
Ip route show
Ip route add default via 172.30.100.20 dev enp0s8
```

```sh
Passwd
Shh root@172.30.120.109
ip -brief link show
reflector -c Germany -a 6 --sort rate --save /etc/pacman.d/mirrorlist
cat /etc/pacman.d/mirrorlist
pacman -Syy
```

# Festplatten-Partitionierung/ Formatierung
```sh
lsblk
gdisk /dev/sda
```

```sh
(gdisk) EFI n Last sector +300M GUID ef00
(gdisk)Swap n Last sector +4G Hex code 8200
(gdisk)Root n Last sector +30G
(gdisk)home n
(gdisk) w
```

# Festplatten-Partitionierung/ Formatierung bios nbr
```sh
fdisk /dev/sda
(fdisk) n p +512M (maximal)
(fdisk) n p +4G
(fdisk) n p +30G
(fdisk) n p (p oder e falls wir mehr betrebsystem haben wollen )
(fdisk) w
```

```sh
mkfs.vfat /dev/sda1
mkswap /dev/sda2
swapon /dev/sda2
mkfs.ext4 /dev/sda3
mkfs.ext4 /dev/sda4
lsblk
```

```sh
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
sda 8:0 0 50G 0 disk
├─sda1 8:1 0 300M 0 part
├─sda2 8:2 0 4G 0 part [SWAP]
├─sda3 8:3 0 25.7G 0 part
└─sda4 8:4 0 20G 0 part
```

## Einhängen der Partitionen
(Zieldateisystem mit /mnt verbinden)
```sh
mount /dev/sda3 /mnt
mkdir -p /mnt/boot/efi
mount /dev/sda1 /mnt/boot/efi
mkdir /mnt/home
mount /dev/sda4 /mnt/home
mkdir -p /mnt/{boot,home}
lsblk
```

```sh
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINTS
sda 8:0 0 50G 0 disk
├─sda1 8:1 0 300M 0 part /mnt/boot/efi
├─sda2 8:2 0 4G 0 part [SWAP]
├─sda3 8:3 0 25.7G 0 part /mnt
└─sda4 8:4 0 20G 0 part /mnt/home
```

# Installation des Grundsystems

```sh
pacstrap /mnt base linux linux-firmware vim git intel-ucode
```

# file systemtable gener
```sh
genfstab -U /mnt >> /mnt/etc/fstab
cat /mnt/etc/fstab
```

# Konfiguration des Basissystems

```sh
arch-chroot /mnt
passwd
date
timedatectl list-timezones | grep Berlin
ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime
hwclock --systohc
date
vim /etc/locale.gen #de_DE.UTF-8 UTF-8 auskommentieren
locale-gen
echo "LANG=de_DE.UTF-8" >> /etc/locale.conf
echo "KEYMAP=de-latin1" >> /etc/vconsole.conf
echo "archefi" >> /etc/hostname
vim /etc/hosts
(hosts)127.0.0.1 localhost
(hosts)::1 localhost
(hosts)127.0.1.1 archefi.localdomain archefi
```

# Boot-Konfiguration
```sh
pacman -S grub efibootmgr networkmanager network-manager-applet base-devel dialog mtools dosfstools wpa_supplicant reflector linux-headers xdg-utils xdg-user-dirs rsync inetutils dnsutils nfs-utils gvfs gvfs-smb openssh xf86-video-intel bluez bluez-utils cups hplip pulseaudio alsa-utils pavucontrol terminus-font
```

# BIOS (nbr)
```sh
pacman -S grub networkmanager network-manager-applet base-devel dialog mtools dosfstools wpa_supplicant reflector linux-headers cups hplip xdg-utils xdg-user-dirs rsync inetutils dnsutils nfs-utils gvfs gvfs-smb openssh xf86-video-intel bluez bluez-utils pulseaudio alsa-utils pavucontrol terminus-font
```

# in bios brauchen kein efibootmgr
```sh
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB
```

# BIOS (nbr)
```sh
grub-install --target=i386-pc /dev/sda
```

```sh
grub-mkconfig -o /boot/grub/grub.cfg

```sh
Konfiguration des Basissystems
systemctl enable NetworkManager
systemctl enable sshd
systemctl enable reflector.timer
systemctl enable systemctl enable bluetooth
systemctl enable cups
```

```sh
useradd -mG wheel Ali
passwd Ali
pacman -S sudo
visudo #Zeile 82 (%wheel) auskommentieren
```

```sh
exit
umount -a
reboot
```
 
