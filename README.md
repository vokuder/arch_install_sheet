# Arch install sheet:

The following steps setup arch linux on an efi-bios with luks encryption.
<br>Please note that the steps below may differ for your system.
<br>So your best bet is to lookup stuff on the official arch wiki.


### 1) Pre install steps:
Set your keyboard layout:
```
loadkeys de-latin1
```
Check your internet connection:
```
ping archlinux.org
```
Set datetime:
```
timedatectl set-ntp true
```


### Create partitions:
```
gdisk /dev/yourHardrive
```
efi partition:
```
new partition: n
partition number (1): enter
first sector: enter
last secotr: +512M
Hex code or GUID: ef00
```
swap partition:
```
new partition: n
partition number (2): enter
first sector: enter
last sector: +32G (Double of your RAM)
Hex code or GUID: 8200
```
root partition:
```
new partition: n
partition number (3): enter
first sector: enter
last sector: enter (reminder of the disk)
Hex code or GUID (8300): enter (default)
```
Apply changes: `w` and accept with `Y`

### Create filesystems:
for efi partition:
```
mkfs.fat -F32 /dev/yourEfiPartition
```
for swap partition:
```
mkswap /dev/yourSwapPartition
swapon /dev/yourSwapPartition
```

### Setup encryption on root partition:
Encrypt:
```
crypsetup -y -v luksFormat /dev/yourRootPartition
```
Decrypt and reopen for further installation steps:
```
crypsetup open /dev/yourRootPartition cryptroot
```
Create filesystem for cryptroot:
```
mkfs.ext4 /dev/mapper/cryptroot
```

### Mount partitions:
Root partition:
```
mount /dev/mapper/cryptroot /mnt
```
Crate boot dir and mount boot partition:
```
mkdir /mnt/boot
mount /dev/yourEfiPartition /mnt/boot
```

### Install linux-kernel and base packages:
For amd: `amd-ucode` for intel: `intel-ucode`
```
pacstrap /mnt base linux linux-firmware nano vim intel-ucode 
```

### Generate partition table:
```
gen fstab -U /mnt >> /mnt/etc/fstab
```

### Change root into the arch system:
```
arch-chroot /mnt
```

### Timezones, locales, keyboard:
Get your timezone:
```
timedatectl list-timezones | grep Berlin
```
Set your timezone:
```
ln -sf /usr/share/zoneinfo/Europe/Berlin /etc/localtime
```
Synchronise hardware clock:
```
hwclock --systohc
```
Uncomment your locale in `/etc/locale.gen` in my case: `de_DE.utf-8 UFT-8` and generate them: `locale-gen`
<br>
<br>Create `locale.conf`
```
echo LANG=de_DE.UFT-8 >> /etc/locale.conf
```
Set the keyboard layout:
```
echo KEYMAP=de-latin1 /etc/vconsole.conf
```

### Hostname and host config:
Hostname:
```
echo pc >> /etc/hostname
```
Hosts: Add the following to `/etc/hosts`
```
127.0.0.1   localhost
::1         localhost
127.0.1.1   pc.localdomain  pc  
```

### Set a password for root user:
```
passwd
```

### Configure grub bootloader:
Install packages using pacman:
```
pacman -S grub efibootmgr
```
Configure `/etc/mkinitcpio.conf`, modify `HOOKS`:
```
HOOKS=(base udev autodetect keyboard keymap modconf block encrypt filesystems fsck)
```
Reconfigure mkinitcpio: `mkinitcpio -p linux`
<br>
<br>
Run grub installation: 
```
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```
Generate grub config:
```
grub-mkconfig -o /boot/grub/grub.cfg
```
Get UUIDs, redirect the output to file to be able to copy paste: `blkid >> uuids`
<br>Then open with: `vim uuids` and press `yy` to copy a line.
Add the root partition uuid in `/etc/default/grub` in the line:
use vim and press `p` to paste, press `ss` to enter insert mode.
```
GRUB_CMDLINE_LINUX="cryptdevice=UUID=copyPastedUUID:cryptroot root=/dev/mapper/cryptroot"
```
<br>
<br>Reconfigure grub config: `grub-mkconfig -o /boot/grub/grub.cfg`


### Create a non root user:
```
useradd -mG wheel <your-name>
```
add a password:
```
passwd <previoulsy-set-username>
```

### Set sudo privileges:
install sudo tools: `pacman -S sudo`
uncomment: `%wheel ALL=(ALL) ALL`

### Exit and reboot:
`exit` -> `umount -a` -> `reboot`


# DONE Hurray
Procced with i3 install sheet.
