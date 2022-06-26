loadkeys trq

ls /sys/firmware/efi/efivars

ping archlinux.org

timedatectl set-ntp true

fdisk -l

fdisk /dev/the_disk_to_be_partitioned

/mnt/boot1	/dev/efi_system_partition	EFI system partition	At least 300 MiB
[SWAP]		/dev/swap_partition		Linux swap		More than 512 MiB
/mnt		/dev/root_partition		Linux x86-64 root (/)	Remainder of the device

mkfs.ext4 /dev/root_partition
mkswap /dev/swap_partition
mkfs.fat -F 32 /dev/efi_system_partition

mount /dev/root_partition /mnt
mount /dev/efi_system_partition /mnt/boot #boot klasörü yoksa oluştur
swapon /dev/swap_partition

pacstrap /mnt base linux linux-firmware vim

genfstab -U /mnt >> /mnt/etc/fstab

arch-chroot /mnt  #chroot oluyoruz

ln -sf /usr/share/zoneinfo/Region/City /etc/localtime

hwclock --systohc

vim /etc/locale.gen
tr_TR.UTF-8 UTF-8
locale-gen

echo "KEYMAP=trq" > /etc/vconsole.conf

echo "LANG=en_US.UTF-8" > /etc/locale.conf

echo myhostname > /etc/hostname

vim /etc/hosts 
/etc/hosts
127.0.0.1        localhost
::1              localhost
127.0.1.1        myhostname

mkinitcpio -P

passwd
---------------------------[Grub kurulumu yapılandırılması]---------------------------
pacman -S grub efibootmgr os-prober

/etc/default/grub
DISABLE_OS_PROBER=false

grub-install --target=x86_64-efi --bootloader-id=grub_uefi --efi-directory=/boot
grub-mkconfig -o /boot/grub/grub.cfg
---------------------------[Gerekli paketler]---------------------------
pacman -sS sudo networkmanager xorg lightdm lightdm-gtk-greeter
pulseaudio xfce4  xf86-video-intel  intel-ucode xfce4-goodies 
wireless_tools
 wpa_supplicant
---------------------------kısa bilgiler---------------------------
xorg = display server
lightdm = display manager
xfce4 = desktop enviroment
---------------------------[Sistemlerin başlatılması]---------------------------
enable NetworkManager.service

/etc/lightdm/lightdm.conf
[Seat:*]
...
greeter-session=lightdm-yourgreeter-greeter
...
enable lightdm.service
status lightdm.service
---------------------------[Kullanıcı Oluşturması]---------------------------
useradd -m -G wheel -s /bin/bash ebubekir
visudo
	%wheel   ALL=(ALL)   ALL
	ebubekirALL=(ALL)  ALL
