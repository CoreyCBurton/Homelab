# Introduction
Proxmox is a hypervisor and will be used to virtualize all my machines. There are two options that most people go with, ESXI (VMware) and Proxmox. Weighing out the benfits and cons of both, I chose Proxmox because its open source and free (unstable versions). This guide will show the steps I have taken to install Proxmox on a Dell Inspiron 3880 desktop.

# Hardware 
The computer hosting Proxmox is a Dell Inspiron 3880 Desktop. The specifications of the computer is below.
 - CPU: 10th Gen Intel® Core™ i7-10700
 - RAM: 12 gb
 - Graphics: Intel UHD Graphics 630 
 
For this homelab, pfSense is going to be virtualized as a router/firewall. A NIC card is installed in the PCI express slot which holds two ethernet ports.  

# NIC Information 
- As mentioned above, one of the reasons why I am using Proxmox is to host pfSense; I want to virtualize my router and firewall. In order to do this, A NIC was installed to hold a LAN and WAN port.
  - This is the NIC card I purchased [[1.25G Gigabit Ethernet Converged Network Adapter (NIC) for Intel 82576 Chip]](https://www.amazon.com/dp/B01LXTF48X?psc=1&ref=ppx_yo2_dt_b_product_details). It goes into the PCI express slot on the motherboard. 
  
- There are two prerequisite that are required for Proxmox to recognize the NIC in the PCI express slot. 
1. Your machine has to be capible of virtualiztion 
2. Your machine also has to support VT-D.
   - In this case, the 10th Gen Intel® Core™ i7-10700 support VT-D

# Instalation of the NIC card
The installation of the NIC was fairly easy, The picture below shows the NIC card used place in the PCI express slot on the motherboard.

<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/NIC_Card_install.jpg" width="500" height="500">

# Enabling VT-D and virtualization
VT-D and virtualiztion has to be enabled via the BIOS. To enter the BIOS on the Dell Inspiron 3880, the user must hit F2 on the boot up screen.
- The picture below shows the two options that enable VT-D and virualization. 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/BIOS_Virtualiztion_Settings.jpg" width="500" height="500">

- The settings are under Virtualization Support. Select enable on both Virtualization and VR for Direct I/O. 

# Formatting a USB to install Proxmox 
- To install Proxmox, A usb can be formatted via an ISO image. For this homelab, Proxmox VE 7.1 was used. 
  - On this [webpage](https://www.proxmox.com/en/downloads), download the Proxmox VE 7.1 ISO Installer. 

**Please note**: Most (newer) processors do not support legacy boot, so therefore programs like [rufus](https://rufus.ie/en/) may not work unless you use DD mode.

The program I used to load the ISO image on a USB is [balenaEtcher](https://www.balena.io/etcher/). This worked for Proxmox VE 7.1.
- The next step is to configure the BIOS to boot USB: UEFI. This can vary from computer to computer. 
   - For the Dell Inspiron, secure boot has to be disabled for the computer to allow USB: UEFI. 

# Steps Installing Proxmox
1. Select: Install Proxmox VE
2. Accept the End User License Agreement (EULA)
3. Set up the target Harddisk
4. Set the Location and Time Zone 
5. Set up the Administrator Password and E-Mail address
6. Set the Network Configuration 
After these steps are done, the Proxmox VE will install.
- Once the install is complete, the user can access the Web interface via ``https://<IP address>:8006/``
  - The web interface can be signed in using root and the password set during the installation. 
 
# Enabling IOMMU
 IOMMU allows the user to "pass" graphics cards or other PCI device to a virtual machine. Since the NIC is in the PCI express slot, this is crucial.
  - The offical documentation to enable IOMMU for Proxmox is [here](https://pve.proxmox.com/wiki/Pci_passthrough#Enable_the_IOMMU)
 
### 1. Connect to the Proxmox box using SSH 
   - Example: ``ssh root@<IP address>``. You can also use the program [PuTTY](https://www.putty.org/) for Windows
 
### 2. Edit the file /etc/default/grub 
- Change the following line GRUB_CMDLINE_LINUX_DEFAUILT to the following depending on your set up
  - AMD: GRUB_CMDLINE_LINUX_DEFAULT="quiet amd_iommu=on"
  - Intel: GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on"
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/Capture.JPG">
 
### 3. Update-grub
   - Use the command ``update-grub``
   - If this step is skipped, the IOMMU will not be recoginized.
 
### 4. Confirm that IOMMU is enabled 
- Use the command ``dmesg | grep -e DMAR -e IOMMU``
  - There should be a line that shows ``DMAR: IOMMU enabled``
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/DmesgDmar.JPG">
 
### 5. Add modules to /etc/modules 
 - Add the following  
```
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```
### 6. Reboot
 
# IOMMU Interrupt Remapping
 - It is possible that your computer does not have support for interrupt remapping; this will cause an error.

 ### 1. Identify if your system has support for interrupt remapping 
 - Use the command ``dmesg | grep 'remapping'``
   - If you see the lines below; it is enabled
   ```
   AMD-Vi: Interrupt remapping enabled
   DMAR-IR: Enabled IRQ remapping in x2apic mode" ('x2apic' can be different on old CPUs, but should still work)
   ```
  - Below shows a picture of the output on my Proxmox server. 
  <img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/dmesgremapping.JPG">
 
 ### 2. If you did not see that remapping is enabled, use this command 
 - Command: ``echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf``
 
# Configuring Proxmox to recognize the LAN and WAN port 
- The NIC has to be recognize by Proxmox and there are two ways to do this.
1. Create a VM on the local machine and configure the hardware to use PCI express
2. Create a network Linux bridge on the local machine (Preferred)

# Hardware to PCI express
- This method works, but some people run into an issue were their machine locks up. With my setup, I faced this issue 
- To do this, go to [VM name] --> Hardware --> Add --> Select both ethernet ports 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/HardwaretoPCI.JPG">

- In the picture above, there are two devices, the ``hostpci0`` and ``hostpci1``
   - **Please Note**: This are each assigned to the two ports. You can see this by 0000.03.00.X
 
# Network Linux Bridge
- This is the method that is perferred to most people. It worked well for this setup. 

 
- Click on local machine --> Network --> Create --> Linux Bridge 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/LinuxBridge.JPG">

 - This is the menu that is brought up, Fill it in according to the user set up
    - Below shows a picutre of how I configured mine.
 
<img src="https://github.com/CoreyCBurton/Homelab/blob/main/Pictures/LinuxBridge2.JPG">

- My setup utilizes enp3s0f0 and enp3s01. 
 - This setup can vary from user to user. The documentation on [netgate.com](https://docs.netgate.com/pfsense/en/latest/recipes/virtualize-proxmox-ve.html) is a great resource. 


 

