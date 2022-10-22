---

# LUKAS' ARCH INSTALLATION & SETUP PROCEDURE

---


                    -@                
                   .##@               
                  .####@              
                  @#####@             
                . *######@            
               .##@o@#####@           
              /############@          
             /##############@         
            @######@**%######@        
           @######`     %#####o       
          @######@       ######%      
        -@#######h       ######@.`    
       /#####h**``       `**%@####@   
      @H@*`                    `*%#@  
     *`                            `*

---

### Last Updated: 2022-08-20

Note: This installation procedure utilizes systemd-boot as its bootloader
as opposed to GRUB. I've made this decision as systemd-boot is more secure,
and is generally better than GRUB, in my opinion.

If anything in this guide does not make sense to you, please consult the
installation guide from the Arch Linux wiki:

https://wiki.archlinux.org/index.php/Installation_guide

### (1) 
After booting from the live USB, connect to the internet. After executing
this command, a GUI will launch. Follow its prompts to connect to wifi.

`wifi-menu`

### (2)
Next, change the password for the root account of the live USB.

`passwd`

### (3)
Now execute the following to refresh Arch's package manager repos.

`pacman -Syy`

### (4) 
At this point, we should refresh pacman's mirrorlist, to speed up the
installation.
    
`pacman -S reflector`

### (5) 
Execute the following to refresh pacman's mirrorlist for your location.
Simply replace Canada with your country of residence.
    
`reflector -c "Canada" -f 12 -l 10 -n 12 --save /etc/pacman.d/mirrorlist`

### (6) 
Now we must install Arch Linux onto the hard drive. Execute the following
and locate your hard drive in the output presented. Take care to locate
the path to the hard drive itself, rather than an existing partition
on the hard drive (e.g. /dev/nvme0n1 as opposed to /dev/nvme0n1p1 for M2
drives, or e.g. /dev/sda as opposed to /dev/sdb for SATA drives).

`fdisk -l`

### (7) 
Now we will use the utility gdisk to create the partition on the hard drive.
Execute the following to open a new gdisk session. 
    
`gdisk [harddrive]`

### (8) Within this new gdisk prompt, certain letters represent certain operations.
To create a new GPT partition table on the hard drive (and thus format it),
execute the following. If you are installing Arch alongside Windows, skip to
step 12, and *do not* execute commands 8, 9, 10, or 11.

`o`

### (9)
Now we will create the EFI (boot) partition. Enter this command to begin the
process of creating this partition.

`n`

### (10) 
Now we will specify the size of this new partition. The first prompt will ask
for the start block - simply press enter whenever this is asked as we want to leave
no gaps in the GPT partition table. For the second prompt, each time use the format
+[INT][GB/MB] to specify what size the partition will be. For this step, we are creating
the EFI partition, or the boot partition. A minimum size of 512MB is reccomended for this,
and the partition should not exceed 1GB.

`+512MB`

### (11) 
Now we will enter the hex code for the type of partition we are trying to
make. In this case, the hex code for an EFI partition is the following.

`EF00`

### (12) 
Now we will create the swap partition. This command will begin the process
of creating this partition by saying we want to create a new partition.

`n`

### (13)
While there is a lot of advice online and otherwise about what size to make swap 
artitions I find that in 2019 it is most appropriate to set your swap partition 
size to approximately half the size of the amount of RAM in your system. Here
I set it to be 8GB, as an example.

`+8GB`

### (14) 
Now we must enter the hex code for the type of partition we are making. In this
case, the hex code for a swap partition happens to be the following.

`8200`

### (15) 
Second to last, we create the root partition. While you may choose to not
seperate your home and root partitions, I reccomend it as it makes the process
of reinstalling your OS and keeping your files intact much simpler, should something
go wrong with your OS or you simply wish to switch to a different distribution.
Enter the following command to initiate the process of creating the new partition.

`n`

### (16) 
Now, we specify the size of the root parition. I reccomend setting it to be somewhere
between 30GB and 100GB, depending on the amount of hard drive space available to you.
We can use the default hex code for the type of partition we are making, so for that
prompt simply press enter.

`+60GB`

### (17) 
Finally, we must create the home partition where personal files will be stored for 
each user. Enter the following command to initiate the process of creating this parition.

`n`

### (18) 
Simply press enter to all of the prompts at this point, to make use of the remaining
hard drive space.

`[Enter]`

### (19) 
Finally, enter the following command to allow gdisk to write the changes you have 
specified to the disk itself.

`w`

### (20) 
Now that the partitions have been made, we will now format the partitions with the
appropriate file format. Execute the following, replacing [EFIpartition] with your
location of your EFI partition (from fdisk -l). If you are installing alongside windows,
do not execute this command, and skip to step 21.

`mkfs.vfat [EFIpartition]`

### (21) 
Next, we will format the root partition with the format ext4.

`mkfs.ext4 [rootpartition]`

### (22) 
Finally, we will format the home partition with the format ext4 as well.

`mkfs.ext4 [homepartition]`

### (23) 
With our hard drive ready for installation of Arch, we will now mount the partitions
to our /mnt directory in the Linux file system, so that we can write directly to these
partitions. First, mount the root partition.

`mount [rootpartition] /mnt`

### (24) 
Now, mount the home partition. If Arch asks if you would like to create the /mnt/home
directory as it does not exist, simply press Enter to agree. If the directory doesn't
exist, simply run mkdir /mnt/home.

`mount [homepartition] /mnt/home`

### (25) 
Now, mount the boot partition. Again, in the case a prompt comes up, simply press Enter.
If the directory doesn't exist, simply run mkdir /mnt/boot.

`mount [bootpartition] /mnt/boot`

### (26) 
With the hard drive ready to write to, execute the following to intall the base
packages of Arch Linux, the optional base-devel package (to make your life easier),
the dialog package (which will allow us to use wifi-menu while setting up the system),
a text editor (in this case, I install emacs, but vim will also do just fine :) ), and
the reflector package, so we can initialize our pacman mirrorlist during setup.

`pacstrap -i /mnt dialog base base-devel emacs reflector`

### (27) 
Now with our base arch installation complete, we will generate our fstab file 

`genfstab -U -p /mnt >> /mnt/etc/fstab`

### (28) 
Now with our system actually setup, we will use what is called a chroot tool to
get into our newly installed system, without restarting the computer.

`arch-chroot /mnt`

### (29) 
I reccomend setting the root password now.

`passwd`

### (30) 
Now, we will install some important packages. While linux and linux-lts are self explanatory,
openssh will help us ssh into the system should we need to in the future and also help us
do common things like generate ssh keys, linux-headers and linux-lts-headers will help us
create custom kernel modules if wanted. linux-firmware contains the most common firmware
needed, and wpa_supplicant, wireless_tools, dhcpcd, and netctl will help us connect to the
internet during the installation process and beyond. Specifically, netctl gives us the util
wifi-menu for quick setup, and dhcpcd gives us quick access to the daemon of the same name
should we want to use an ethernet connection during install instead.

`pacman -S openssh linux linux-headers linux-lts linux-lts-headers linux-firmware wpa_supplicant wireless_tools dhcpcd netctl dialog`

### (31) 
Next, we must generate our locale. Locale represents your region. Open the /etc/locale.gen
file with your text editor of choice, and uncomment your locale (i.e. delete the # from the
line #en-US.utf-8 if you are american, or en-CA.utf-8 if you are Canadian, etc.).

`emacs /etc/locale.gen`

#### OR

`vim /etc/locale.gen`

### (32) 
Now execute the following command to generate your locale, and the command listed after to set
your computers' hostname

`locale-gen`
`hostnamectl set-hostname [desired computer name]`

### (33) 
Soft link locale to local time (replace with your personal location)

`ln -s /usr/share/zoneinfo/Canada/Mountain /etc/localtime`

### (34) 
Install and start the ntp daemon, which will keep your system time in sync. Note that if you are dual booting, you will
have to change your windows settings to avoid conflicts in general between Linux and Windows.

`pacman -S ntp`
`systemctl enable ntp`
`systemctl start ntp`

### (35) 
Enable ssh (reccomended but not required)

`systemctl enable sshd.service`

### (36) 
Install bootctl

`bootctl install`

### (37) 
cd into loader directory and edit loader.conf config file with chosen text editor

`cd /boot/loader`
`emacs loader.conf`

### (38) 
Enter the following into the loader.conf file and save the file

`default arch`
`timeout 5`

### (39) 
cd into entries directory and edit arch.conf config file with chosen text editor

`cd entries`
`emacs arch.conf`

### (40) 
Enter the following into arch.conf and save the file. In [output of blkid], you
must enter the partition identifier for your root partition, which is listed
in the output of blkid. Directions for how to obtain the output of this command
and read it into the file are found in brackets below for emacs and vim.

```
title ArchLinux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=PARTUUID=[raw output of blkid root partition code, no quotes] rw
(in vim - Esc :r !blkid [ENTER], in emacs, C-u M-x shell-command blkid [ENTER])
```

Now we need to confirm that our entries are valid before rebooting.
To do so, run the command:

`bootctl`

And confirm that there are no warnings, such as the no file called vmlinuz-linux
which will occur if you forgot the command earlier:

`pacman -S linux`

(41) Exit the chroot environment and unmount the partitions, then reboot into the installed
system (remove the USB drive containing the live environment after rebooting)

```
exit
umount /mnt
reboot
```

### (42) 
Log into the system as root and set locale

`localectl set-locale LANG="en_CA.UTF-8"`

### (43) 
Add a user account for your personal use

`useradd -m -g users -s /bin/bash [username]`

Then connect to the internet:

`wifi-menu`

If unable to use wifi-menu to connect to the internet, opt for an ethernet connection and
execute the following instead:

```
systemctl enable dhcpcd
systemctl start dhcpcd
```

### (44) 
Install the sudo command and use visudo to add yourself to the sudoers group by adding
a line under the line specifying the rules for root with an identical line, only using
your username instead of "root".

```
pacman -S sudo
visudo
```

Then set a password for your user so that you can log in to the desktop environment later:

`passwd [username]`

### (45) 
Install vital packages for automatic network management

`pacman -S networkmanager network-manager-applet`

### (46) 
Install vital packages for xorg and graphical display drivers and backup drivers like mesa and vesa

`pacman -S xf86-input-libinput xorg-server xorg-xinit xorg-apps mesa xf86-video-intel xf86-video-vesa`

If you are running and nvidia card, you might want to install that driver now and configure x to use
that also:

```
pacman -S nvidia
nvidia-xconfig
```

### (47) 
Install the sddm display manager and the plasma KDE desktop environment. Alternatively, you can use
the gnome-desktop DE and gdm display manager to run gnome, etc.

```
pacman -S sddm
pacman -S plasma
```

Optional, but provides many helpful GUI apps:

`pacman -S kde-applications`

### (48) 
Enable the sddm display manager for initialization upon startup

`systemctl enable sddm`

### (49) 
Enable the network manager service for wireless access

`systemctl enable NetworkManager.service`

If you previously enabled the dhcpcd service, disable it now as NetworkManager will take care of
automatic network management:

`systemctl disable dhcpcd`

Then reboot into your system:

`reboot`

------------------ OPTIONAL STEPS ------------------

### (49) 
Printer setup

```
[login]
[open terminal GUI app, e.g. konsole]
sudo pacman -S cups system-config-printer
sudo systemctl enable cups
sudo usermod -aG sys [username]
[open printers kde application to configure]
```

### (50) 
Install pamac pacman GUI app and enable AUR support within it

```
cd /tmp
sudo pacman -S git
git clone https://aur.archlinux.org/pamac-aur.git
cd pamac-aur
makepkg -si

[open pamac]
[enable AUR]
```

### (51)
Install `yay`, a cli option for managing AUR packages, if desired:

```
cd /opt
sudo git clone https://aur.archlinux.org/yay-git.git
sudo chown -R <type username here>:<type username here> ./yay-git
cd yay-git
makepkg -si
```

### (52) 
Install helpful AUR kde packages using pamac or yay:

```
[install kde-gtk-config]
[install libappindicator-gtk2 and libappindicator-gtk3, etc.]
```
