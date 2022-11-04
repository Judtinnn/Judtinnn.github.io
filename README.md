## Arch Linux Installation Documentation

### 1. Downloading installation image for Arch Linux
- Within the [Arch Linux installation wiki](https://wiki.archlinux.org/title/Installation_guide), navigate to the list of [mirrors](https://archlinux.org/download/) for United States and select any of the mirrors, then download the ISO file. For example, I chose the rackspace.com mirror and downloaded archlinux-2022.11.01-x86_64.iso file.

### 2. Setting up VM Ware
- Open Vm Ware.
- Click "Create a New Virtual Machine".
- Click "Custom" and go through the process of customizing your VM Ware.
  - Hardware compatibility: Should be set to the highest, which is "Workstation 16.2.x".
  - Installer disc image file (iso): Find and select your ISO file.
  - Guest operating system: Select "Linux".  
      Version: Choose "Other Linux 5.x kernel 64-bit.
  - Virtual machine name: "Arch Linux".
  - Number of processors: "1".  
    Number of cores per processor: "2".
  - Memory for this virtual machine: Set it to 2 GB, "2048".
  - Select "Use network address translation (NAT)".
  - SCSI Controller: "LSI Logic".
  - Virtual disk type: "SCSI".
  - Select "Create a new virtual disk".
  - Maximum disk size (GB): "20".  
    Select "Store virtual disk as a single file".
  - Disk file: "Arch Linux.vmdk".
  - Click "Finish".
- Under "My Computer" on the left, right click "Arch Linux" and click "Open VM directory". 
  - Open "Arch Linux.vmx" file.
  - Insert the code ```firmware="efi"``` on line two.
  - Save the changes and exit out of the files and folders.

### 3. Starting up VM Ware
- Click the green arrow start button and select the "efi" mode (the top option) and press enter. 
- The default console keymap is US, but that is always not the case.  
  Just in case, set the keyboard layout to US by running this command ```loadkeys us```.
- Verify the boot mode by running the command ```ls /sys/firmware/efi/efivars```.  
  If there are no errors the system is in UEFI mode. 
  
### 4. Partitioning the disk and formatting the partitions
- Use the command ```fdisk -l``` (dash letter l, not dash number one) to see which disks you can use.  
  The disk I can use is /dev/sda. Disks ending with loop can be ignored (/dev/loop0).
- Use the command ```fdisk /dev/the_disk_to_be_partitioned``` to **modify partition** table. For example, I did ```fdisk /dev/sda```.
- Command ```m``` can be used to see the list of commands that can be executed.  
  **Important commands to note is:** 
  - ```n``` adds a new partition
  - ```p``` prints the partition table
  - ```d``` deletes a partition
  - ```w``` write table to disk and exit
- Create two partition: /dev/efi_system_partition and /dev/root-partition. 
  - First partition (/dev/efi_system_partition):
    - Run ```n``` command to add partition. 
    - Set partition type to primary and keep partition number as default (1). 
    - First Sector is set as default (2048).
    - Last Sector should at least be 300 MiB. I used 500 MiB, using the command ```+500M``` (2048 MiB + 500 MiB).
  - Second partition (/dev/root_partition):
    - Run ```n``` command again. 
    - Set partition type to primary. 
    - Partition number, first sector, and last sector are all set to default. 
- Use command ```p``` to print out your partitions and see if your two partitions were created correctly.  
  There should be two devices, /dev/sda1 (first partition) and /dev/sda2 (second partition).  
  If the two partition seems like it was printed correctly, save and exit by running the command ```w```.
- To format each partition use ```mkfs.ext4 /dev/root_partition``` and ```mkfs.fat -F 32 /dev/efi_system_partition```.  
  For example, ```mkfs.ext4 /dev/sda2``` and ```mkfs.fat -F 32 /dev/sda1```. 

### 5. Mounting the file systems. 
- To mount the root volume, run this command ```mount /dev/root_partition /mnt```.  
  Use this command ```mount --mkdir /dev/efi_system_partition /mnt/boot```  mount the EFI system partition.  
  For example, ```mount /dev/sda2 /mnt``` and ``` mount --mkdir /dev/sda1 /mnt/boot```.
- The command ```lsblk -f``` can be used to check if the partitions were mounted correctly.

### 6. Installing packages
- Use this command ```pacstrap -K /mnt base linux linux-firmware nano grub dhcpcd efibootmgr sudo``` to install packages you need.  
  You at least need base package and linux and linux-firmware hardware. 
  
### 7. Configuring the system
- Use ```genfstab -U /mnt >> /mnt/etc/fstab``` command to generate fstab file. 
- Change the root by running this command ```arch-chroot /mnt```. 
- Set the time zone by using this command ```ln -sf /usr/share/zoneinfo/America/Chicago /etc/localtime```.  
  Then run this command ```hwclock --systohc```.
- Edit /etc/locale.gen by running this command ```nano /etc/local.gen``` and uncomment en_US.UTF-8 UTF-8 (remove the #).  
  To save the file, ctrl + X and press Y and press Enter. 
- Now run ```locale-gen```.
- To create the locale.conf file, use the ```touch /etc/locale.conf``` command and ```nano /etc/locale.conf``` to edit the file.  
  Set the language variable by adding this line ```LANG=en_US.UTF-8``` and save the file. 
- To make the console keyboard layout changes visible (from earlier), create /etc/vconsole.conf file and edit it by adding ```KEYMAP=us```.   
  Make sure to save the file. 
- Create the hostname file by creating /etc/hostname file and edit it by adding ```JJK```. Then save the file. 
- Edit etc/hosts by running ```nano etc/hosts``` and edit the file by adding ```127.0.0.1 localhost ::1 localhost 127.0.1.1 JJK``` (save the file).
- Recreate the initramfs image by running ```mkinitcpio -P```. 
- Set the password by running ```passwd```. 
- Install these packages ```pacman -S man-pages texinfo sudo man-db sof-firmware dosfstools amd-ucode wpa_supplicant wireless_tools networkmanager nm-connection-editor     network-manager-applet grub```.
- To install bootloader run ```grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB```.  
  Then run these two commands ```grub-mkconfig -o /boot/grub/grub.cfg``` and ```mount /dev/sda1 /mnt```.
  - **Issue**: Had an error when running this command ```grub-mkconfig -o /boot/grub/grub.cfg```, so I tried reinstalling grub using ```grub-install``` and pacman.  
    After using this command ```grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB``` it started to work. 
- Now run ```exit``` to exist out of the root and run ```reboot``` to reboot the system. 
- Input the login (root) and password.

### 8. Creating user accounts
- Justin's user account
  - ```useradd -m -g users -s /bin/bash justin``` creates a user justin.  
    ```-m``` creates the user's home directory, ```-g``` adds the user to the regular users group, ```-s /bin/bash``` sets the bash as the user's default shell. 
  - ```passwd david``` creates a password for user justin.
- Codi's user account
  - ```useradd -m -g users -s /bin/bash codi``` creates a user codi.
  - ```passwd codi``` creates a password for codi (GraceHopper1906). 
- Giving users sudo permission
  - Run this command ```visudo```  
    **Issue:** Error occured when running ```visudo``` command  
    **Error:** ```visudo: no editor found (editor path = /usr/bin/vi)```  
    **Solution:** Do this instead to access visudo 
    - Find the location of your text editor by running this command ```whereis text_editor```.  
      For example, I used nano as my text editor, so I did ```whereis nano``` and the location of it is ```/usr/bin/nano```
    - Then you run this command to access visudo ```EDITOR=location_of_text_editor visudo```.  
      E.g. ```EDITOR=/usr/bin/nano visudo```.
  - Scroll down to ```root ALL=(ALL:ALL) ALL```
  - Underneath it, type ```justin ALL=(ALL:ALL) ALL```.  
    Below that type ```codi ALL=(ALL:ALL) ALL```
  - To save and exit nano, press ctrl + X, then press Y, then press enter. 

### 11. Installing a different shell (zsh)
- To install zsh package run this command ```pacman -S zsh```
- 

### 10. Installing desktop environment (KDE) 
To install a desktop environment, you need a working display server and an appropriate display driver.  
I will be installing xorg for the display server. 

