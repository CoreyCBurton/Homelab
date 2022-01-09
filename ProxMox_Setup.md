# Introduction
The reason why I am installing Proxmox on this homelab is to virtualize all my machines. There are two options that most people go with, ESXI (VMware) and Proxmox. Weighing out the benfits 
benefits of both, I chose Proxmox because fulfills most things I require and its free (unstable version). This guide will show the steps I have taken to have a functioning hyper visor.

# Hardware 
I used the Dell Inspiron 3880 Desktop as the computer to host Proxmox. The specifications of the Dell computer are below.
 - CPU: 10th Gen Intel® Core™ i7-10700
 - RAM: 12 gb
 - Graphics: Intel UHD Graphics 630 

# NIC Information 
- One of the reasons why I am using Proxmox is to host pfSense. I want to virtualize my router and firewall. In order to do this, I need a dual NIC card. This is going to hold the WAN and LAN
ports. 
- This is the NIC card I purchased. It goes into the PCI express 
  - [1.25G Gigabit Ethernet Converged Network Adapter (NIC) for Intel 82576 Chip](https://www.amazon.com/dp/B01LXTF48X?psc=1&ref=ppx_yo2_dt_b_product_details)
- Since this NIC goes into the PCI express slot on the motherboard, there are two things that are required.
1. Your machine has to be capatabile of virtualiztion 
2. Your machine also has to support VT-D so the PCI NIC can be recognized.
   - In this case, the 10th Gen Intel® Core™ i7-10700 support VT-D

# Instalation of the NIC card
The installation of the NIC was fairly easy, The picture below shows the slot it was place on the motherboard of the Inspiron 3880. 
- <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/NIC_Card_install.jpg" width="500" height="500">
There is more configuration to do later in Proxmox. 

# Enabling VT-D on a Dell computer 
To enter the BIOS on this machine, the user must hit F2. The picture below shows the menu where they can enable both options needed.
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/BIOS_Virtualiztion_Settings.jpg" width="500" height="500">
- The settings are under Virtualization Support. Select enable on both Virtualization and VR for Direct I/O 

# Installing ProxMox as the OS 
Newer processors do not support legacy boot, so therefore programs like [rufus](https://rufus.ie/en/) may not work unless you use DD mode.
- I recommend using the program balenaEtcher, it works for the installation of ProxMox
- Boot from the USB using UEFI

## Steps for the Instaltion screen for Proxmox
- 
