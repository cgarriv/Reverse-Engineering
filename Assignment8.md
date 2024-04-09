## Summary of NjRAT:

NjRAT is a Remote Access Trojan that is used to maliciously remotely control infected machines. NjRAT is one of the most widely used Remote Access Trojans worldwide due to its abundant educational information and YouTube tutorials (Anyrun). NjRAT is also know by its aliases; Bladabindi and Njw0rm. The RAT first appeared at the start of the year in 2013 with some variants of the malware appearing in the winter of 2012 (Gridinsoft). Even with such longstanding, NjRAT remains among the top 10 most widespread threats as of today. NjRAT is capable of executing remote shell commands, modify files including uploading and downloading, capture screenshots and recordings as well as logging keystrokes and has access to the machine's camera/microphone. njRAT makes use of phishing scams to spread its malware onto machines. It can also use injections from the help of loaders. More common methods of spread have been observed through sophisticated spoofing of legitimate softwares that infect the machines once it has been downloaded and executed. In addition, sophisticated spam campaigns where detected in 2016 through Discord. The functionality of the RAT includes complex methods to avoid detection and evade static checks used in most antivirus programs in addition to the code itself being particularly difficult to analyze and understand to both humans and machines (Gridinsoft). NjRAT also has the capability to detect if it is being ran in a Virtual Environment, which aids the attackers in setting up counter measures against research and reverse engineering. This along with its capabilities once it is executed makes the RAT highly effective in gaining access, staying undetected, and compromising user's worldwide. 

**Works Cited:**

- Anyrun: https://any.run/malware-trends/njrat
- Gridinsoft: https://gridinsoft.com/backdoor/njrat


## Dynamic Analysis Using RegShot and FakeNet-NG:

As always it is crucial to use extreme caution and attentiveness when analyzing any malware. To conduct this analysis I will be operating within a controlled environment using a Windows 10 Virtual Machine through VMware. The first step is to take a snapshot of the machine prior to executing the malware. 

- Once the snapshot has been taken. It is important to double check that the VM is completely isolated both from the internet and the host machine. Next, we have to simulate a fake internet by creating a new network adapter. This can be done within the windows 'Add a New Adapter' setting within 'Network Connections'. 
	- Next, install a loopback adapter to simulate a network interface. 
	- Configure the New Network Adapter, assign a static IP to 'Internet Protocol Version 4 (TCP/IPv4)' and adjust the settings accordingly under 'Properties'.
	- Set the DNS Server under the same properties. 
	- Ensure network isolation by testing connectivity in a web browser.
	- Ensure the fake network is functional. In a command terminal, ping the network and ensure the connectivity is functional. 
	- Last, launch FakeNet-NG and make sure it is correctly communicating with the simulated network.

- The next step is to open RegShot and take the first shot prior to executing the RAT.
- Once the shot has been created, it is time to execute the malware, but first, ensure one last time that the VM is completely isolated.
- Execute the NjRAT.exe file. Give it a few minutes to run and make changes.
- Next, in FakeNet-NG look for any changes within the communication of simulated network to ensure that the malware is running correctly. In addition, check your Task Manager to see the activity and ensure that NjRAT is running. 
- Then, open RegShot and take the second shot and save the detected changes to a text file.
- Once RegShot has completed its process it will give you a dialog box that included the changes in the registry keys, including, values added, values deleted, keys added, keys deleted, etc.

## RegShot:


## Significant Registry Keys Deleted:


```HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\ServiceInstances```

This registry key deals with group policy settings that provide centralized management and configuration of operating systems, applications, and users' settings.

The deletion of this registry key implicates that the RAT is attempting to alter service behaviors or disable security policies.

The RAT appears to be deleting the keys to weaken the security or prevent the enforcement of policies that might restrict the RAT's operations. 

![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/c8d8f2ac-14f3-41d2-859b-4b53f263bbd0)

## Significant Registry Keys Added:

```HKLM\SOFTWARE\Microsoft\IdentityCRL\ThrottleCache\S-1-5-18_{fc177c6f-a3d6-xxxx-xxxxxxx}```

This key is related to Microsoft Identity services, where CRL stands for Certificate Revocation List. It is part of the infrastructure that supports sign-in and authentication services for Microsoft products.

If this key is added maliciously it can implicate that the RAT is interfering with the normal throttling and caching mechanisms. This in-turn will affect the reliability or security of the authentication processes. 

Furthermore, since the key is associated with Local System account (S-1-5-18), the malware can also be attempting to elevate privileges or compromise the systems security. RAT's often times target or mimic system or service-related keys to blend in with legitimate processes. 

**These registry keys are added to ensure it starts automatically with every system boot:**
```HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run```

```HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer\Run```

**This Registry Key is added to ensure persistence:**

```HKLM\SYSTEM\CurrentControlSet\Services```

This key modification was added after checking a specific location, in this case the System32 Folder in the Local C drive. 

![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/93d072ae-7adf-43c4-ace8-385e980ac664)

- The research on the registry key changes provide clear indicators and clues of compromise. in particular to the type of changes being made in effort to avoid detection, escalate privileges, and the modification of registry keys for persistence upon detection and deletion.
- After gather the information needed, I was curious to see what would happen if i simply killed the task in the task manager, doing so caused a complete crash of the virtual machine. Upon rebooting, the malware was running again and continued to talk to the fake network. 

## FakeNet-NG:

Doing analysis to observe the changes in network requests and communications I noticed that there was a constant request from DNS Server for domain: ```zaaptoo.zapto.org```
FakeNet-NG also saves a .pcap file to due further analysis with Wireshark.
Opening the file I was able to filter by DNS activity and got a better view of the communication with this domain.

![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/f8c0c4c4-724b-46e6-b104-fd73b66ca145)
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/19c9bef9-cb43-41c1-a524-b88e2fb30124)
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/68927f13-78e0-4fb4-a8af-e9d3169ae727)

### Zapto:

Doing research on the domain Zapto I was able to find multiple sources that indicate that this domain is malicious. The site is considered 'High Risk' according to AnyRun, ipquality.com, and virustotal.com. The domain also appears to be temporary, disposable, and likely used for abuse and for hosting malware and remote access, and command and control communications. It is held as 'Highly Confident' for being used for conducting abusive behavior including scams, malware hosting and distribution.
The domain's hostname is: ```mail2.no-ip.com```
With and IP address of: ```69.65.5.119```
And tied to the ISP and organization of: ```GigeNET```
- Further details can be found at: https://www.virustotal.com/gui/domain/zapto.org/details

- Another interesting discovery made about the domain zapto is that it hosted on a dynamic DNS which implicates that the domain allows the malware operators to frequently change IP addresses while maintaining a consistent domain name for infected machines to report back to. 

- Doing the research on the domain the deduced information clearly provides indicators and clues of compromise that a network administrator can easily identify. 
