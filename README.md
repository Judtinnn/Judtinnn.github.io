## Arch Linux Installation Documentation

### 1. Downloading installation image for Arch Linux
- Within the [Arch Linux installation wiki](https://wiki.archlinux.org/title/Installation_guide), navigate to the list of [mirrors](https://archlinux.org/download/) for United States and select any of the mirrors, then download the ISO file. For example, I chose the rackspace.com mirror and downloaded archlinux-2022.11.01-x86_64.iso file.

### 2. Setting up VM Ware
- Open Vm Ware
- Click "Create a New Virtual Machine"
- Click "Custom" and go through the process of customizing your VM Ware
  - Hardware compatibility: Should be set to the highest, which is "Workstation 16.2.x"
  - Installer disc image file (iso): Find and select your ISO file
  - Guest operating system: Select "Linux"  
      Version: Choose "Other Linux 5.x kernel 64-bit
  - Virtual machine name: "Arch Linux"
  - Number of processors: "1"  
    Number of cores per processor: "2"
  - Memory for this virtual machine: Set it to 2 GB, "2048"
  - Select "Use network address translation (NAT)"
  - SCSI Controller: "LSI Logic"
  - Virtual disk type: "SCSI"
  - Select "Create a new virtual disk"
  - Maximum disk size (GB): "20"  
    Select "Store virtual disk as a single file"
  - Disk file: "Arch Linux.vmdk"
  - Click "Finish"
- Under "My Computer" on the left, right click "Arch Linux" and click "Open VM directory" 
  - Open "Arch Linux.vmx" file
  - Insert the code firmware="efi" on line two
  - Save the changes and exit out of the files and folders.

### 3. Starting up VM Ware
- Click the green arrow start button and select the "efi" mode (the top option) and press enter
  
