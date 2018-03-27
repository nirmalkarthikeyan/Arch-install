# Arch-install-UEFI
This is just a list of instructions, not an executable file. Please don't download and execute! 

1) Power on and boot into the live installer.

2) Check your disk partitioning using 'fdisk -l' and note down the partition numbers of the root and EFI partitions.

3) Format the root partition to a format linux supports, preferably ext4.

4) Mount the root partition in the directory /mnt using the command 
`# mount /dev/sdax /mnt`

5) If using swap, initialize it using 
`# mkswap /dev/sdxY`
`# swapon /dev/sdxY`

6) Format the EFI partition (whose number we noted earlier using 'fdisk-l') to FAT32 by:
`# mkfs.fat -F32 /dev/sdxY`

7) Make a directory for the esp - /mnt/boot.
`# mkdir -p /mnt/boot`
(the -p is to make it a parent directory if it is absent, ie like creating a new folder)

8) Mount the esp to the previously created directory
`# mount /dev/sdxY /mnt/boot`

9) Now is the actual installing bit.
`# pacstrap -i /mnt base base-devel`
(base devel is required for AUR support and stuff)

10)Now, an 'fstab' file has to be generated, which lists the partitions used by the OS.
`# genfstab -U /mnt >> /mnt/etc/fstab`
(-U is for UUIDs, -L can also be used for Labels instead)

11)chroot into the Arch install.
'# arch-chroot /mnt'

12)Open the locale file: 
'# nano /etc/locale.gen'

13)Uncomment the locale you require (the wayland server is wrecked if this is improper)

14) Create a locale.conf:
'# echo LANG=en_US.UTF-8 > /etc/locale.conf'
'# export LANG=en_US.UTF-8'
(Replace the locale with the one you selected - they should match)

15) Set the timezone:
'# ln -s /usr/share/zoneinfo/Asia/Kolkata /etc/localtime'
'# hwclock --systohc --utc' 
//sets the system clock, doesn't work on certain HP laptops.

14)Get the sudo package and the wifi stuff.
'# pacman -S grub wireless_tools wpa_supplicant wpa_actiond dialog'

15)Add a root password:
'# passwd'
(type and retype)

16)Create a new user:
'# useradd -m -g users -G *all required permissions -s* /bin/bash *username*'

17)Add a passwd for *username*
'# passwd *username*'

18)Edit the sudo file to give required permissions:
'# visudo'

19) Uncomment the line that gives the permissions you think are appropriate for your usage.
Eg:
'# %wheel ALL=(ALL) ALL'
(widely accepted permissions!)
Uncomment - remove the pound symbol.

20)GRUB (Grand Unified Botloader) time
Grab it from the repos: 
'# pacman -S grub efibootmgr fuse2 os-prober'

Install GRUB: 
'# grub-install --target=x86_64-efi --efi-directory=*esp* --bootloader-id=*arch_grub*'
Any id can be used, and esp is the path where the esp is mounted ie */mnt/boot* here.

21)Generate a grub-config file.
'# grub-mkconfig -o *esp*/grub/grub.cfg'
where *esp* is 'mnt/boot' for me.

22)Give a hostname:
'# echo *preferredname* > /etc/hostname'

23)Done! Exit chroot, unmount all drives and reboot!

'# exit
'# umount /dev/sdxY'
'# reboot'

*I'll add the graphics, sound, usability stuff in the evening

