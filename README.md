# usbraid

Items:
- Raspberry Pi 3
- DLink Usb Hub
- 4 x Intenso 16G Usb stick

install mdadm on Raspberry Pi 3:
sudo parted /dev/sda set 1 raid on

create partition schema on all usb sticks:
sudo parted /dev/sda mklabel msdos
sudo parted /dev/sdb mklabel msdos
sudo parted /dev/sdc mklabel msdos
sudo parted /dev/sdd mklabel msdos

create partition on every usb sticks:
sudo parted -a optimal -- /dev/sda mkpart primary 2048s -8192s
sudo parted -a optimal -- /dev/sdb mkpart primary 2048s -8192s
sudo parted -a optimal -- /dev/sdc mkpart primary 2048s -8192s
sudo parted -a optimal -- /dev/sdd mkpart primary 2048s -8192s

mark partition as raid-partition on every usb stick:
sudo parted /dev/sda set 1 raid on
sudo parted /dev/sdb set 1 raid on
sudo parted /dev/sdc set 1 raid on
sudo parted /dev/sdd set 1 raid on

create Raid 5 on the usb-sticks:
sudo mdadm --create /dev/md0 --auto md --level=5 --raid-devices=4 /dev/sda1 /dev/sdb1 /dev/sdc1 /dev/sdd1

check size of raid:
sudo fdisk -l /dev/md0

check status of raid initializing:
cat /proc/mdstat 

to install a crypted file system on raid install cryptsetup:
sudo apt-get install cryptsetup

crypt raid partition: 
sudo cryptsetup luksFormat -c aes-xts-plain64 -s 512 -h sha512 /dev/md0

decrypt/load raid/luks partition:
sudo cryptsetup luksOpen /dev/md0 luks

create ext4 filesystem on raid/luks partition:
sudo mkfs.ext4 /dev/mapper/luks

mount ext4 filesystem:
mount /dev/mapper/luks /mnt


 