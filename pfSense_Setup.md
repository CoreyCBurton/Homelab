# Introduction
Now that Proxmox is installed, the ISO of pfSense can be added to the server to create the router.

# Install the pfSense ISO image
- The pfSense ISO image can be found [here](https://www.pfsense.org/download/)
  - This homelab is using the latest version 2.5.2 

- Navigate to the Proxmox web GUI and follow this path to upload the image; Server --> local domain --> ISO images --> Upload 
  - Below is the picture of the location and name of the ISO I am installing 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/ISO%20image%20install.png" width="500" height="500">

# Verify network cards 
- pfSense is going to require two ethernet ports. One if for LAN and the other is for LAN. While setting up Proxmox, a dual NIC was installed through the PCI express slot 
  - Below is the screenshot my the network setup. The path is server --> Network 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Network%20Cards.png" width="500" height="500">
- There are two Linux bridges set up. vmbr0 and vmbr1. 
  - vmbr0 is using the interface enp3s0f0 which is under the IP 192.168.1.96/24. This is what are router is connected to and will hold the **WAN**
  - vmbr1 is uging the interface enp3s0f1. This is what is going to be pluged into the switch and will hold the **LAN**

# Set up the pfSense VM 
- Now that everything is setup, it is time to create the VM 
  - Below is a screenshot of what I used to create the VM. It will vary from machine to machine. 
 <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/pfSense%20VM%20setup.png" width="500" height="500">
- Turn on start up at boot. This is important because since it is your router and firewall, it need to be added. The path is server --> options --> start at boot --> yes
  - Below is a screenshot of my settings 
  - <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Start%20up%20at%20boot.png" width="500" height="500">

- Go to pfSense VM --> Hardware --> Add --> Network device  
  - Make sure both network devices are added. Below shows the settings that both vmbr0 and vmbr1 are added to pfSense machine.
  - <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Hardware%20Network%20Devices%20.png" width="500" height="500">

# Start up the VM
- Select Install
- <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Install%20pfSense.png" width="500" height="500">


- Use the default keymap 
- <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/DefaultKeymap.png" width="500" height="500">


- Auto (UFS) BIOS
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/UFS%20boot%20install.png" width="500" height="500">


- No manual install is needed 
- <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/manual.png" width="500" height="500">


- Reboot the computer 
- <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Reboot.png" width="500" height="500">

# Setup pfsense
- When pfSense is booted up, there is a screen that shows both that the LAN and WAN have been assigned to the interfaces (vmbr0 vmbr1)
- 




   

