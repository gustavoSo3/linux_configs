# My personal arch install Guide

## First verfy UFI

```bash
ls /sys/firmware/efi/efivars
```

## Confirm I have internet conection

```bash
ping google.com
```

### If I dont have a conection try

```bash
ip link
ip link set /dev/{interface} up
dhcpcd /dev/{interface}
```

## Set system clock

```bash
timedatectl set-ntp true
```

## Now partition disks

### I can see my devices with

```bash
fdisk -l
```

### And I start a partition on a disk using

```bash
fdisk /dev/{disk}
```

Here, press letter 'm' to get the menu.
I press 'g' to make a new GPT partition table.
I press 'n' to make a new partition.

> I can see the menu all the times I want to not press the wrong key.

If I forget, I can chek _[this guide](https://www.youtube.com/watch?v=4Uc3-PIq-80)_

#### Make partition tables

- I will use a GPT partition
- And create the following partitions
  - boot: size:512M, type: 'EFI System'
    - > For boot stuff
  - root: min-size:20GB + space for Swap-File, type: 'Linux root (x86-64)'
    - > Where linux and all packages will install.
  - home: size: All the space reminding, type: 'Linux home'
    - > My home partition

I press 't' to select the partition type, and 'L' to show the types I can choose.
When i finish press 'w' to write the partition table to the disk.

#### Format the partitions

For boot

```bash
mkfs.vfat /dev/{boot_partition}
```

For root and home

```bash
mkfs.ext4 /dev/{partition}
```

## Mount partitions

### First, mount root partition

```bash
mount /dev/{root_partition} /mnt
```

### Make directory and mount the other partitions

```bash
mkdir /mnt/boot
mount /dev/{boot_partition} /mnt/boot
```

```bash
mkdir /mnt/home
mount /dev/{home_partition} /mnt/home
```

### Make swap file

```bash
fallocate -l {???}GB /mnt/swapfile
chmod 600 /mnt/swapfile
mkswap /mnt/swapfile
swapon /mnt/swapfile
```

## Install ARCH

```bash
pacstrap /mnt base base-devel linux linux-firmware vim intel-ucode networkmanager dhcpcd
```

And wait

## Generate fstab

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

## Change Root

```bash
arch-chroot /mnt
```

## Set time

```bash
ln -sf /usr/share/zoneinfo/America/Mexico_City /etc/localtime
hwclock --systohc
```

## Set locale

```bash
vim /etc/locale.gen
```

Uncomment

> #en_US.UTF-8 UTF-8

```bash
vim /etc/locale.conf
```

Write

> LANG=en_US.UTF-8

## Set Hostname

```bash
vim /etc/hostname
```

> Write
>
> > arch

```bash
vim /etc/hosts
```

> Write
>
> > 127.0.0.1 localhost
> > ::1 localhost
> > 127.0.1.1 arch.localdomain arch

## Set Root password

```bash
passwd
```

> Password

## Install extra packages

```bash
pacman -S man-db man-pages texinfo wget curl inetutils linux-headers git
```

## Set bootloader

```bash
pacman -S grub efibootmgr os-prober
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

Si no jala poner

GRUB_DISABLE_OS_PROBER=false

en 

/etc/default/grub

## finalize

### Set networkmanager service

```bash
systemctl enable NetworkManager
```

```bash
exit
unmount -a
reboot
```

## Next Steps

Up to here I have a full working base arch install with only the root user.
I have a white canvas :)

## Add a user

### Requires

```bash
pacman -S sudo vi
visudo
```

> Uncomment
>
> > %wheel ALL=(ALL) ALL

### Create user

```bash
useradd -m {user_name}
passwd {user_name}
usermod -aG wheel,audio,video,optical,storage {user_name}
```

> Change to user with
>
> > ```bash
> > su {user_name}
> > ```

## Must install

- nvidia

## Apps lists

- Firefox
- Alacritty
- Feh
- Steam
- ```bash
  git clone https://aur.archlinux.org/yay.git
  cd yay
  makepkg -si
  ```

## Install window manager

- Posibilities
- [x] i3-gaps
- [ ] dwm

### Instalation

```bash
sudo pacman -Syu i3-gaps
```
