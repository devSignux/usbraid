# usbraid

Items:
- Raspberry Pi 3
- DLink Usb Hub
- 4 x Intenso 16G Usb stick

install mdadm on Raspberry Pi 3:
<pre>
sudo parted /dev/sda set 1 raid on
</pre>

create partition schema on all usb sticks:
<pre>
sudo parted /dev/sda mklabel msdos
sudo parted /dev/sdb mklabel msdos
sudo parted /dev/sdc mklabel msdos
sudo parted /dev/sdd mklabel msdos
</pre>

create partition on every usb sticks:
<pre>
sudo parted -a optimal -- /dev/sda mkpart primary 2048s -8192s
sudo parted -a optimal -- /dev/sdb mkpart primary 2048s -8192s
sudo parted -a optimal -- /dev/sdc mkpart primary 2048s -8192s
sudo parted -a optimal -- /dev/sdd mkpart primary 2048s -8192s
</pre>

mark partition as raid-partition on every usb stick:
<pre>
sudo parted /dev/sda set 1 raid on
sudo parted /dev/sdb set 1 raid on
sudo parted /dev/sdc set 1 raid on
sudo parted /dev/sdd set 1 raid on
</pre>

create Raid 5 on the usb-sticks:
<pre>
sudo mdadm --create /dev/md0 --auto md --level=5 --raid-devices=4 /dev/sda1 /dev/sdb1 /dev/sdc1 /dev/sdd1
</pre>

check size of raid:
<pre>
sudo fdisk -l /dev/md0
</pre>

check status of raid initializing:
<pre>
cat /proc/mdstat 
</pre>

to install a crypted file system on raid install cryptsetup:
<pre>
sudo apt-get install cryptsetup
</pre>

crypt raid partition: 
<pre>
sudo cryptsetup luksFormat -c aes-xts-plain64 -s 512 -h sha512 /dev/md0
</pre>

decrypt/load raid/luks partition:
<pre>
sudo cryptsetup luksOpen /dev/md0 luks
</pre>

create ext4 filesystem on raid/luks partition:
<pre>
sudo mkfs.ext4 /dev/mapper/luks
</pre>

mount ext4 filesystem:
<pre>
mount /dev/mapper/luks /mnt
</pre>

reload after start:
<pre>
sudo mdadm --assemble --auto=yes --scan
sudo cryptsetup luksOpen /dev/md0 luks
sudo mount /dev/mapper/luks
</pre>



 