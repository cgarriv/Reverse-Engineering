# CS479/579 Reverse Engineering at NMSU

# Summary:
The purpose of this repo is to hold reports on reverse enguneering malware samples from "Practical Malware Analysis".

# System Setup:

Safety and Precautions:

For the purposes of safety and precaution a reverse engineering lab will be setup and utilized throughout the semester to insure the malware being analyzed is isolated and unable to infect host machines, networks, and potential other devices connected to the network. 

System Isolation:

To further add a layer of protection, a *NIX-based hypervisor will be utilzed to run a virtual machine with a windows 11 operating system. In case of accidental malware leak or escape, it is unlikely that the *NIX system will also be compromissed. 

Network Isolation:

In addition to the previous safety measures, networking capabilities will be eliminated from the Windows VM to further prevent any accidental malware leaks or escapes.

*NIX-based Hypervisor:

To start, I will be using a Lenovo computer and booting Kali Linux from a USB drive. To succesfully achieve this I will need to first flash Kali Linux Live Boot onto the flash drive using Rufus. It is important to set a persistent partition to have access to storage capabilities. 
Once Kali Linux is succesfully flashed onto the USB drive, we then boot our host device into BIOS. In my case this will be done by rapidly selecting F2 after turning on the device. Once in BIOS we will adjust the boot priority to boot from USB drive and also disbale secure boot.
Once that is done, your device will boot into the Kali Linux start up menu where we select "Live System with USB persistance".
At this point, we have succesfully created our host *NIX device. 

Windows 11 Virtual Machine:

The next step in our lab setup is to deploy a virtual machine running windows 11 OS. 
We will be downloading VMware Player 17 for linux onto a SSD with sufficent storage space. 
Once the file is loaded on to the SSD to be able to access and install the program from the SSD we need to run Linux terminal as an administrator and make the file executable by giving the terminal these commands

1. -chmod +x [FILE NAME]
2. -sudo ./[FILE NAME] -install

We have now succesfully installed VMware onto our Kali Linux host machine. 
Next, we need to create a virtaul machine with a Windows 11 OS.
To start, we need to download the Windows 11 ISO from Microsoft and install it onto our SSD. 
Once the ISO is downloaded we then luanch VMware and create a new VM by installing the ISO from the SSD into the new VM, VMware makes the setup easy and guides you along the installation.
Finally, we need to have sufficent swap space partiented on our SSD for the VM to luanch successfully. To do this simply follow this guide:

https://askubuntu.com/questions/178712/how-to-increase-swap-space

Once the swap space has been increased we are able to succesfully luanch or new Virtual Machine running Windows 11 as the victim device.
At this point, it is important to take a snapshot of the current state of the virtual machine. This allows us to revert back to this snapshot that is free of malware. 

Setting up Windows 11 for Reverse Engineering:

After succesfully running the VM with Windows 11 installed. We need to install a few applications and tools that we will utilize to analyze malware. 
First, install Visual Studio with a C compiler. This can simply be done by following the instructions on Microsofts webpage. 

To be able to succesfully install the following tools and be able to run malware we first need to permanently disable Windows Defender on Windows 11
Under the Windows Security menu in Windows 11, select the manage setting options for Virus and Threat Protection Settings and disable Tamper Protection.
Next, open the Registry Editor application. Follow this folder selction 

HKEY_LOCAL_MACHINE -> SOFTWARE -> Policies -> Microsoft -> Windows Defender.

Once in this folder, right click to create a new "DWORD (32-bit) Value" Name it "DisableAntiSpyware" Once created, double click on it and set the value data to 1.


Repeat the process and create "DisableRealtimeMonitoring" giving it a value of 1 also.

Repeat the process and create "DisableAntiVirus" giving it a value of 1 also.

Repeat the process and create "DisableSpecialRunningModes" giving it a value of 1 also

Repeat the process and create "DisableRoutinelyTakingAction" giving it a value of 1 also

One more, Repeat the process and create "ServiceKeepAlive" leaving this one as defualt with value of 0.

In the interest of brevity and clarity, the rest of the instructions on how to successfully disable Windows Defender can be found here:

https://www.youtube.com/watch?v=v8-ikrGijTs

Once Windows Defender is permanently disabled, we can now install the additional tools necessary for analyzing malware.

Follow the instructions on Mandiant's webpage to install VM-Flare

https://www.mandiant.com/resources/blog/flare-vm-the-windows-malware

Finally, it is important to remove any network capabilities for your Windows 11 VM.

This can be easily done in your hypervisor's VM settings. 



