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
- Create two partition: /mnt/boot partition and /mnt partition. 
  - First partition (/mnt/boot):
    - Run ```n``` command to add partition. 
    - Set partition type to primary and keep partition number as default (1). 
    - First Sector is set as default (2048).
    - Last Sector should at least be 300 MiB. I used 500 MiB, using the command ```+500M``` (2048 MiB + 500 MiB).
  - Second partition (/mnt):
    - Run ```n``` command again. 
    - Set partition type to primary. 
    - Partition number, first sector, and last sector are all set to default. 
- Use command ```p``` to print out your partitions and see if your two partitions were created correctly.  
  There should be two devices, /dev/sda1 and /dev/sda2.  
  If the two partition seems like it was printed correctly, save and exit by running the command ```w```.
- 
