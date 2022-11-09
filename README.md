# rootfs-arm
How to build (add needed packages) via rootfs for ARM

Building Ubuntu rootfs for ARM

00. Download Ubuntu core
download address from http://cdimage.ubuntu.com/cdimage/ubuntu-base/releases/14.04/release/

01. Generate the image
sudo dd if=/dev/zero of=rootfs.img bs=512 count=1024*10
//kernel use the label linuxroot to mount the rootfs as /
sudo mkfs.ext4 -F -L linuxroot rootfs.img
sudo mount -o loop rootfs.img /mnt

02. Uncompress tar file
sudo tar zxvf ubuntu-*.tar.gz -C /mnt

03. The step is only for ARM base
sudo apt-get install qemu-user-static
sudo cp /usr/bin/qemu-arm-static /mnt/usr/bin/

04. Set up dns
sudo cp /etc/resolv.conf /mnt/etc/

05. Change root
sudo mount -t proc /proc ~/ubuntu-rootfs/proc
sudo mount -t sysfs /sys ~/ubuntu-rootfs/sys
sudo mount -o bind /dev ~/ubuntu-rootfs/dev
sudo mount -o bind /dev/pts ~/ubuntu-rootfs/dev/pts
sudo chroot /mnt

06. Install applications/tools/comands
sudo apt-get update
sudo apt-get install \
  language-pack-en-base \  
  sudo \
  ssh \
  net-tools \
  wpasupplicant \
  wireless-tools \
  ifupdown \
  network-manager \
  iputils-ping \
  rsyslog \
  htop \
  vim \
  xinit xorg \
  alsa-utils \
  --no-install-recommends

07. Set up config
// add user
useradd username

//set up hostname
vim /etc/hostname

//add localhost
vim /etc/hosts
127.0.0.1  localhost
127.0.1.1  hostname  (or modify /etc/hostname)

//Make X used by ‘anyuser’
vim /etc/X11/Xwrapper.config
allowed_users=anybody

//set up timezone
dpkg-reconfigure tzdata
//set up group for alsa
vim /etc/group
audio:x:29:pulse,username
// Set up group for fbterm
vim /etc/group
video:x:44:username
08. for Chinese
sudo sed -i '$azh_TW.UTF8 UTF-8' /var/lib/locales/supported.d/local
sudo locale-gen zh_TW.UTF-8
sudo update-locale LANG=zh_TW.UTF-8
sudo update-locale LANGUAGE=zh_TW.UTF-8

sudo apt-get -y install \
language-pack-zh-hant \
language-pack-gnome-zh-hant \
language-pack-zh-hant-base \
language-pack-gnome-zh-hant-base \
language-selector-common \
language-selector-gnome
09.
sudo umount ~/ubuntu-rootfs/proc
sudo umount ~/ubuntu-rootfs/sys
sudo umount ~/ubuntu-rootfs/dev/pts
sudo umount ~/ubuntu-rootfs/dev


Thats all - system ready
