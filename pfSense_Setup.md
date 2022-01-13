# Introduction
Now that Proxmox is installed, the ISO of pfSense can be added to the server to create the router. There are steps to set up the pfSense which this guide will go through as adding the network cards vmbr0 and vmbr1, installing pfSense, and creating the WAN and LAN interfaces. 

# Step 1: Install The pfSense ISO Image
- The pfSense ISO image can be found [here](https://www.pfsense.org/download/).
  - This homelab is using the latest version 2.5.2. 

- Navigate to the Proxmox web GUI and follow this path to upload the image; **Server --> local domain --> ISO images --> Upload**.
  - Below is a screenshot of the location the ISO will be installed.
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/ISO%20image%20install.png">

# Verify Network Bridges Are Configured  
- pfSense is going to require two ethernet ports; one is for the LAN and the other is for the WAN. While setting up Proxmox, a dual NIC was installed through the PCI express slot.
  - Below is the screenshot what needed to be added to configure the LAN and WAN. 
  - The path to these settings can be found here; **Server --> Network** 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Network%20Cards.png">

- There are two Linux bridges that are set up. vmbr0 and vmbr1. 
  - vmbr0 is using the interface enp3s0f0 which is under the IP 192.168.1.96/24. This is what the router is connected to and will hold the **WAN**
  - vmbr1 is using the interface enp3s0f1. This is going to be plugged into the cisco switch and will hold the **LAN**

# Setting up the pfSense VM 
- Now that the Linux bridges are set up, it is time to configure the pfSense VM. 
  - Below is a screenshot of the settings I used to configure pfSense.
  - This will vary from machine to machine. 
 <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/pfSense%20VM%20setup.png">
 
 ## Turn on at boot 
- One important step is to turn on pfSense when the server restarts. 
  - This is important because since it is your router and firewall, it needs to be up while the server is up. 
  - The path to set this options is **Server --> Options --> start at boot --> yes** 
  <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Start%20up%20at%20boot.png">

# Adding the Network devices (vmbr0 and vmbr1) to the pfSense VM.
- In order for pfSense to recongnize the Linux bridges, some settings need to be configured. 
- Go to **pfSense VM --> Hardware --> Add --> Network device**  
  - Make sure both network devices are added (vmbr0 and vmbr1). Below shows the settings that both vmbr0 and vmbr1 are added to pfSense machine.
  <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Hardware%20Network%20Devices%20.png">

# Start up the VM
## The first step is to select install on the pfSense VM
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Install%20pfSense.png">

## it will then ask you to setup the default keymap
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/DefaultKeymap.png">


## For my VM, I used Auto (UFS) BIOS as the partition
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/UFS%20boot%20install.png" width="500" height="500">


## For this lab, any manual install is not needed needed 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/manual.png">


## Rebooting the computer is necessary
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Reboot.png">

# Setting up pfSense
- When you first launch up pfSense, it is going to ask if you would like to setup VLANS. 
  - For this lab, it is not required so typing in ``n`` will bypass this.
- It is then going to ask which interfaces will be the LAN and WAN. my set up follows this below. 
  - **WAN**: vtnet0
  - **LAN**: vtnet1
- It will then ask you to proceed as shown in the screenshot below.
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/pfsense%20Interfaces%20.png">

# Assigning the WAN and LAN ip addresses 
- pfSense is then going to assign you a WAN and LAN ip on each interface.
  - The screenshot below shows my set up 
- <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/LAN%20and%20WAN%20initial%20setup.png">
- It was assigned to 
  - WAN: 192.168.1.?
  - LAN:192.168.1.1

# Changing the IP address of the LAN 
- I would like my lab to be under the subnet 192.168.0.100/24, so I will change it accordingly by typing in 2.
  - It will then ask if you would like to edit the WAN or the LAN. Since I would like to edit the LAN, option 2 will be selected.
  - It will then ask to enter the IPv4 address 192.168.0.100
  - The CIDR notation used will be /24
  - The steps are shown below. 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/LAN%20setup%20.png">

# Setting up the DHCP Server
- I would like to set up a DHCP server for connected devices. 
  - pfSense will ask for you to set up one. I want the DHCP addresses to be assigned from 192.168.0.101 - 192.168.0.200,
  - The steps are shown below. 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/DHCP%20server.png">




   

