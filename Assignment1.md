# Summary
For this assignment the goal was to work in a *NIX enviorment along side the GCC C-Compiler. The objective of this assignment was to use the linux system to call exec and replace the current running process with a terminal shell (/bin/sh)
Several files were provided to assist in the assignment including a makefile, a C file called shellcode_tester.c, and a python file called x_shellcode.py.
Our challange was to write assembly code (shellcode.S) to use the syscall instruction to ask the OS to execute the shell at /bin/sh using execve.

# My assembly code:

.global _start
.text

_start:

# zero-out rax
xor %rax, %rax 
# null byte
push %rax
# /bin/sh in hex in LIFO
mov $0x68732f6e69622f2f, %rbx
push %rbx

# pointer to /bin/sh
mov %rsp, %rdi
# zero-out rax
xor %rax, %rax
# Null byte
push %rax
# Pointer to /bin/sh for arguement array
push %rdi
# Pointer to arguement array
mov %rsp, %rsi
# system call value
mov $59, %rax
# null
xor %rdx, %rdx

syscall 

# Code Breakdown

 xor %rax, %rax: the first instruction is to clear or zero out the register %rax by XORing it with itself, providing a null byte.
 
 push %rax: pushes a null byte onto the stack and will be used to terminate string /bin/sh

 mov $0x68732f6e69622f2f: %rbx will move the hex value representation of /bin/sh in lifo order into register %rbx.

 push %rbx:pushes the string value /bin/sh onto the stack

 mov %rsp, %rdi: setting up the first arguement, rsp points to the top of the stack, where /bin/sh is located, this moves the stack pointer into rdi where the filename of execve is expected

 xor %rax, %rax: again nullifies the register to push onto the arguement array

 push %rax: pushes onto the arguement array

 push %rdi: pushes the pointer to /bin/sh in rdi on to the stack

 mov %rsp, %rsi: second arguement, moves the current stack pointer and is used for the execve system call

 mov $59, %rax: execve systerm call number in Linux

 xor %rdx, %rdx: Zero out or clear for third arguement 

 syscall: invokes the system call.

 # Total Bytes

 Running x_shellcode.py gave me the results of:

 "Generated shellcode is of length: 38
 4831c05048bb2f262696e2f7368534889e74831c050574889e648c7c03b0000004831d20f05"
 

 

 

 

 
 

 
