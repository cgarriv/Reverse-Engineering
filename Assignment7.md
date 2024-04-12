### Summary
- This week we will be observing library injections in action.
- A DLL injection is a technique used for running code within the address space of another process by forcing it to load a dynamic-link library (DLL).
- This method is often used in malicous attacks that inject malware into legitimate processes to hide malicous actions or gain elevated privallages.

#### 1. Prove that the loader is using DLL injection. (Don't forget a relevant snapshot in Ghidra.)

- The loader Lab12-01.exe is using the function 'LoadLibraryA' from Kernel32.dll or user32.dll by using 'GetProcAddress' and injects the DLL into a sub process. This is observed in the 'CreateRemoteThread' from the programs interface used to communicate between the two files. The starting address of the thread is set to the address of 'LoadLibraryA'.
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/fe10b083-ff5a-4378-a7a4-b747e60b8c34)
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/61110aba-68e4-43a5-afa9-c280ce29f4aa)

#### 2. Identify the process that will be injected into. Seeing a string in Ghidra isn't sufficient -- explain how the process gets selected.

- The process for injection is used by searching for 's_explorer.exe_00406030' it is selected based on the existence of the explorer.exe in the running processes list from the functions in 'EnumProcessModules' and 'GetModuleFileNameA' from the DLL psapi.dll which is dynamically loaded and used to verify the process name.
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/cb523fd6-3e09-4e41-90ef-d6a3e9dd243f)
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/2ff6dd01-9b2c-40a9-a8b0-cbbc2fc7583a)
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/e35e460a-b9c8-4009-92bc-0a98e18792aa)

#### 3. Identify the entry point of the DLL injection. Where is DllMain?

- The entry point of the injected DLL is the standard initialization function 'DllMain' called by Windows when the DLL is loaded into a process from a load call from 'LoadLibraryA' and injected by 'CreateRemoteThread'.
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/3a6b594b-1b4a-41fb-b827-2391e42ca696)



#### 4. This malware does something every ______ seconds. How often, and where is the loop where that waiting happens?

- The malware has an infinite loop with a sleep interval of 60000 milliseconds or 60 seconds. This is seen in the 'Sleep' function within the Lab12-01.dll file where it creates a messageboxA and calls Sleep(60000).
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/8623bbf7-2ad1-4169-9537-02eae7b2a631)

#### 5. What does the malware do every _______ seconds?

- Every 60 seconds the malware displays a message box reading "Practical Malware Analysis %d" where the count is iteratively increased by 1 each loop. In the message box it reads "Press OK to reboot" however, the button does not have functionality.
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/00b399a9-74b3-4da4-bc6a-9413ad749e69)


### References:

- Help and guidance was used to understand the malware. For reference I used the tutorial provide by YouTuber "Andy Marks" 
- Link to the video: https://www.youtube.com/watch?v=mkntkr5Je0g
- Andy was using IDA and used a couple different methods to analysis the malware. This tutorial was not used to as a step by step instructional but rather a point of reference to attempt the analysis on my own.
- In addition, I used ChatGPT to ask questions when I did not fully understand something.
