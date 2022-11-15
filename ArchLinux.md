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
  ![Users with Sudo Priviledge](https://user-images.githubusercontent.com/113713588/200100681-798fce8a-c5c9-4e7c-b62d-c9fdc59ecb78.png)

### 9. Installing desktop environment (KDE) 
To install a desktop environment, you need a working display server and an appropriate display driver.  
I will be installing xorg for the display server. 
- **Issue:** Couldn't install any packages using pacman. After few tests, found out that ```ping google.com``` doesn't work,  
  which means that there is probably an issue with my internet connection.  
  **Solution:** Ran these two commands ```sudo systemctl disable dhcpcd.service``` and ```sudo systemctl start NetworkManager.service```, and it started working. 
- To install xorg run this command ```pacman -S xorg xorg-server```
- Keep all dependencies to default (press enter) and proceed with the installation (press Y)
- Then run ```pacman -S xf86-video-intel``` (only works for intel).  
  If you are using AMD, replace "intel" with "amdgpu".  
  If you are using Nvidia, replace "intel" with "nouveau".  
  If you are unsure, you can replace "intel" with "vesa". It will download the very basic package for you. However, it is not recommended to stay with this. 
- Install appropriate display manager. I will be installing KDE Plasma 5, so the recommended display manager to install is ```pacman -S sddm```
- Finally to install the desktop environment (KDE plasma 5), run this command ```pacman -S plasma kde-applications```.
- Keep all the customizations to default (press enter) and proceed with the installation (Y). 
- Run this command to enable the display manager ```systemctl enable sddm```
- Reboot to get your new desktop environment, ```reboot```
![KDE Desktop Environment](https://user-images.githubusercontent.com/113713588/200100866-a23f4c34-97f5-422b-8497-cc0f6d94f494.png)

### 10. Installing a different shell (zsh)
- Issue: Another error with installing packages using pacman. Same error so use the same solution as on "9. Installing desktop environment (KDE)".
- To install zsh package run this command ```pacman -S zsh```
- Run ```zsh```
- Press ```1``` to continue to the main menu
- Press ```2``` and it will configure the auto-complete system. 
- Press ```1``` to set it to default options.
- Press ```0``` to exit and save. 
- Type ```exit``` to get back to the terminal. 
- To customize my zsh shell, I will be installing "Oh My Zsh".
  - **Issue:** Couldn't install Oh My Zsh because Git was not installed. Use this command to install Git ```pacman -S git```
  - To install oh my zsh run this command ```sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"```
  - Press Y if it asks to change the default shell to zsh. 
  - Then run ```nano ~/.zshrc```
  - Scroll down to ```ZSH_THEME="robbyrussell"``` and comment it out using #
  - Underneath it insert this command ```ZSH_THEME="agnoster"```.
  - Save and exit by pressing CTRL+X, then Y, then enter.
- If it didn't ask you earlier to change the default shell to zsh, run this command ```chsh -s /usr/bin/zsh```.
- Type ```exit``` to get back to the terminal
![ZSH shell with oh my zsh](https://user-images.githubusercontent.com/113713588/200101065-07e1e517-8c2f-4e03-9f65-6c814d4610b8.png)

### 11. Installing ssh and ssh into class gateway
- If you haven't already, update your package repository with ```pacman -Syu```. 
- Install the ssh server using ```pacman -S openssh```
- Check if your open ssh is running using ```systemctl status sshd```.  
  If it says "inactive" then it is not running. 
- If it is inactive, then run this command ```systemctl start sshd``` and check if it is active using the previous command.  
  If you ever need to stop the ssh server, then run this command ```systemctl stop sshd```
- If you automatically start the ssh server once the system reboots, run this command ```systemctl enable sshd```.  
  Run this command to stop automatically starting the ssh server ```systemctl disable sshd```
- Run this to ssh into the class gateway ```ssh -p port user@server-address```.  
  E.g. ```ssh -p 22 sysadmin@129.244.245.111```
![SSH into class gateway](https://user-images.githubusercontent.com/113713588/200101104-92e4d9a5-62b1-40c5-b440-832f7aeb0d8d.png)

### 12. Color coding
- Run this command to go edit the zshrc file ```nano ~/.zshrc```
- Input these two commands ```autoload -U colors && colors``` and ```PS1="%{$fg[red]%}%n%{$reset_color%}@%{$fg[blue]%}%m %{$fg[yellow]%}%~ %{$reset_color%}%% "```.
- Red, blue, green, cyan, yellow, magenta, black, and white are basic colors you can use to customize (some copmuters may have more options). 
![Color coding](https://user-images.githubusercontent.com/113713588/200100615-ee9211f8-6c33-4c20-b67f-070adf943755.png)


### 13. Aliasing
- Run this command to go edit the zshrc file ```nano ~/.zshrc```
- ```alias c='clear'``` Shortcut to clear
- ```alias rb='sudo reboot'```Shortcut to reboot 
- ```alias ls='ls --color=auto'``` Color code ls output
- ```alias grep='grep --color=auto'``` Color code grep output
- ```alias egrep='egrep --color=auto'``` Color code egrep output
- ```alias ping='ping -c 5'```Stop after sending count ECHO_REQUEST packets
![Aliasing in ZSH](https://user-images.githubusercontent.com/113713588/200101135-dfe4116e-252c-4198-9426-4f4a0a487f32.png)

### 14. Booting in GUI
- Check step 9. Installing desktop environment (KDE).

### Additional customizaton 
- ```pacman -S firefox``` to install firefox.
- Installed "oh my zsh" to customize my shell (instructions in 10. Installing a different shell (zsh)).
- Changed the zsh theme to "agnoster".
- Other customizations may have been performed that are not mentioned here. 
