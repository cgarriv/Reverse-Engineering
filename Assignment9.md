# Anti Virus Program for NjRAT


The functionality of my anti-virus program that detects and removes NjRAT is based on being able to detect NjRAT running in windows processes. It firsts looks for any processes named 'NjRAT.exe', 'NjRAT.exe (32 bit)', 'windows.exe', 'windows.exe (32 bit)'. If any of those process names are found it will indicate the compromise to the user. Next, the program will check for files in specified paths. Similarly, if the files are found it will indicate compromise.

Following the functionality of the 'Scan' button. We then use the 'Remove' button to one, kill the processes that are identified as the RAT, and two, locate and delete the specified file names within the specified locations provided through the file paths.  

Through my dynamic analysis of NjRAT I had the knowledge that NjRAT will disguise itself in the processes as windows.exe. Through the task manager's startup menu I was also able to see the windows.exe in the the startup process as well as a new process `ecc7c8c51c0850c1ec247c7fd3602f20.exe` When attempting to manually delete the files I was met with an error 'Unable to delete: file is in use by windows.exe', this gave me the knowledge that I first needed to kill the process in my program. 

### Screen Recording Showing Functionality:

- My VM is extremely slow and I did not want to edit the video to avoid any doubts of integrity.
- I would advice to skip around and jump any periods of delay due to the lag of the VM. And/Or watch in 2.0x speed. 

https://www.dropbox.com/scl/fi/qab93mgv5jxqh78tpzzxr/NjRATAntiVirus.mp4?rlkey=trmt50buvw1jgr5xca00p3ggf&st=no64r4yj&dl=0

### Functional .exe (AntiVirusNjRAT.exe)

-click the dropbox link to download the .exe
-it will say 'file type not supported here' click the download button
-it will prompt you to login, click download without account.

https://www.dropbox.com/scl/fi/ba5me4tz4ft0pk7ibsp8t/AntiVirusNjRAT.exe?rlkey=02fm3brwybfphl8g78p31xgjc&st=12jgfcq0&dl=0

### Anti Virus Program Source Code:

```
import tkinter as tk
from tkinter import messagebox
import psutil
import os


def scan_for_njrat():

    process_names = ["windows.exe (32 bit)", "windows.exe", "NjRAT.exe", "NjRAT (32 bit)"]
    file_paths = ["C:\\Temp\\windows.exe",
                  "C:\\Users\\vmdev\\AppData\\Local\\Temp\\windows.exe",
                  "C:\\Users\\vmdev\\AppData\\Roaming\\Microsoft\\Windows\\Start Menu\\Programs\\Startup\\windows.exe",
                  "C:\\Users\\vmdev\\AppData\\Roaming\\Microsoft\\Windows\\Start Menu\\Programs\\Startup\\ecc7c8c51c0850c1ec247c7fd3602f20.exe" ]

    found_processes = []
    found_files = []

    # Scan malicious processes
    for proc in psutil.process_iter(['pid', 'name']):
        if proc.info['name'] in process_names:
            found_processes.append(proc.info['name'])

    # Scan malicious files
    for path in file_paths:
        path = os.path.expandvars(path)
        if os.path.exists(path):
            found_files.append(path)

    if found_processes or found_files:
        messagebox.showinfo("Scan Results",
                            f"Malicious processes found: {found_processes}\nMalicious files found: {found_files}")
    else:
        messagebox.showinfo("Scan Results", "No indicators of compromise found.")


def remove_njrat():
    success = True
    message_details = []

    # terminate malicious processes
    for proc in psutil.process_iter(['pid', 'name']):
        if proc.info['name'].lower() in ["windows.exe (32 bit)", "windows.exe", "NjRAT.exe", "NjRAT (32 bit)"]:
            try:
                proc.terminate()
                proc.wait(timeout=3)  # Wait up to 3 seconds for the process to terminate
                message_details.append(f"Terminated Process: {proc.info['name']}")
            except psutil.NoSuchProcess:
                message_details.append(f"Process Terminated: {proc.info['name']}")
            except psutil.AccessDenied:
                message_details.append(f"Access Denied: {proc.info['name']}")
                success = False
            except psutil.TimeoutExpired:
                message_details.append(f"Timeout Expired, Termination Failed: {proc.info['name']}")
                success = False

    # remove malicious files
    file_paths = ["C:\\Temp\\windows.exe",
                  "C:\\Users\\vmdev\\AppData\\Local\\Temp\\windows.exe",
                  "C:\\Users\\vmdev\\AppData\\Roaming\\Microsoft\\Windows\\Start Menu\\Programs\\Startup\\windows.exe",
                  "C:\\Users\\vmdev\\AppData\\Roaming\\Microsoft\\Windows\\Start Menu\\Programs\\Startup\\ecc7c8c51c0850c1ec247c7fd3602f20.exe" ]

    for path in file_paths:
        path = os.path.expandvars(path)
        try:
            if os.path.exists(path):
                os.remove(path)
                message_details.append(f"Removed file: {path}")
            else:
                message_details.append(f"File not found: {path}")
        except Exception as e:
            message_details.append(f"Failed to remove {path}: {str(e)}")
            success = False

    if success:
        final_message = "NjRAT removal successful.\n"
    else:
        final_message = "NjRAT removal encountered errors.\n"

    final_message += "\n".join(message_details)
    messagebox.showinfo("Removal Results", final_message)


# GUI
root = tk.Tk()
root.title("Anti-NjRAT Tool")

# Scan
scan_btn = tk.Button(root, text="Scan for NjRAT", command=scan_for_njrat)
scan_btn.pack(pady=20)

# Remove
remove_btn = tk.Button(root, text="Remove NjRAT", command=remove_njrat)
remove_btn.pack(pady=20)

# Run the application
root.mainloop()
```
