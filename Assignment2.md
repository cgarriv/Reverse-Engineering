## Summary:

The purpose of assignment 2 is to preform a static analysis on a malware file. For this assignment we will be using CFF Explorer to analyze the file Win32.KeyPass.bin. A brief examination of the file indicates the malware is passing as a .bin file to avoid detection in security filters or antivirus systems that examine files with .exe or .dll more thoroughly. The malware also appears to be ransomeware file encryptor that exploits windows operating systems. 

## Operating System Targeted:

• Windows OS

### Virus or Worm:

• The file appears to be a trojan virus, does not appear to be self-replicating and requires human action to execute. 

### Imports:

MPR.dll
gdiplus.dll 
IMM32.dll 
COMCTL32.dll 
WINMM.dll 
USER32.dll 
WINSPOOL.DRV 
GDI32.dll 
MSIMG32.dll 
ADVAPI32.dll

### Compiler:

[C++] VS2013

### Payload:

• The files suggest that it is a payload as it appears it is injecting into other processes specifically to encrypt user files and recording keystrokes and screen captures to capture sensitive data such as passwords and other access credentials. 

### MITRE Techniques:

• Keylogging: The malware uses keylogging methods to gain credential access 

• File and Directory Discovery: Discovers, reads, and encrypts user's files.

• Defense Evasion: Encoded data using XOR, Process Injection - creates a process to inject code, Modifies registry. 

• Data Encryption: Encrypts data and prints file to screen demanding ransom.

### Signature:

SHA-1: D9BEB50B51C30C02326EA761B5F1AB158C73B12C
MD5: 6999c944d1c98b2739d015448c99a291

### Other Indicators:

Resource Editor displays a folder with icons that display a Bitcoin icon. Indicating that this is a ransomware exploit that collects ransom in Bitcoin. 

### Indicators of Compromise:

#### Import Directory:

This malware can be recognized by the .DLL imports it uses. IMM32 is commonly used in keylogging. 

USER32 contains functions for windows management and can be used to capture user input

ADVAPI32 is used in persistence, privilege escalation and modifying security settings. 

### Clues of Origin:

Commonly contacts: [192.229.221.95], [193.200.255.58] 

Domains: Kronus.pp.ua

Domains: bitmessage.ch,india.com,kronus.pp.ua,onus.pp.ua,schemas.microsoft.com

Hosts: 141.95.47.146,2.20.143.104

URL: hxxp://kronus.pp.ua/upwinload/get.php

Whois Lookup: 

```Organization: [Edgecast Inc. (EDGEC-25)](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%2522Edgecast%2520Inc.%2520(EDGEC-25)%2522)

RegDate: [2013-02-07](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A2013-02-07)

Updated: [2022-07-11](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A2022-07-11)

Ref: [https://rdap.arin.net/registry/ip/192.229.128.0](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%2522https%253A%252F%252Frdap.arin.net%252Fregistry%252Fip%252F192.229.128.0%2522)

OrgName: [Edgecast Inc.](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%2522Edgecast%2520Inc.%2522)

OrgId: [EDGEC-25](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253AEDGEC-25)

Address: [13031 W Jefferson Blvd. Building 900](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%252213031%2520W%2520Jefferson%2520Blvd.%2520Building%2520900%2522)

City: [Los Angeles](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%2522Los%2520Angeles%2522)

StateProv: [CA](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253ACA)

PostalCode: [90094](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A90094)

Country: [US](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253AUS)

RegDate: [2022-04-26](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A2022-04-26)

Updated: [2024-01-08](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A2024-01-08)

Ref: [https://rdap.arin.net/registry/entity/EDGEC-25](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%2522https%253A%252F%252Frdap.arin.net%252Fregistry%252Fentity%252FEDGEC-25%2522)

OrgTechHandle: [KLEIN349-ARIN](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253AKLEIN349-ARIN)

OrgTechName: [Kleinart, Shawn](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%2522Kleinart%252C%2520Shawn%2522)

OrgTechPhone: [+1-602-850-4845](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253A%252B1-602-850-4845)

OrgTechEmail: [skleinart@edg.io](https://www.virustotal.com/gui/search/entity%253Aip%2520whois%253Askleinart%2540edg.io)```




