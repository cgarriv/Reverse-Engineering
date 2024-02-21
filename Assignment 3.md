# Summary
Week 4 assignment tasks us with solving our first crackme. In this assignment we will be using IDA and Ghidra to solve a crackme by figuring out the password it is expecting. In addition, we will also be reinforcing some C/Assembly concepts.

# Assembly Review

1. What is the difference between machine code and assembly?

	• Machine code is a set of binary instructions that executed by a computer's CPU directly and is difficult or complex to read for humans but simple for computers and machines. Assembly on the other hand is more human-readable and is specific to a computer's architecture like machine code but with use of memory address, registers, and instructions that are then compiled into binary to be executed by the CPU. 

2. If the ESP register is pointing to memory address `0x00000000001270A4` and I execute a `pushq rax` instruction, what address will `rsp` now be pointing to?

	• When the pushq rax instruction is executed, the CPU will then place the value in rax onto the stack. rsp will then be decremented on the stack by 8 bytes and will now point to 0x000000000012709C

3. What is a stack frame?

	• A stack frame is a portion of the stack memory used to store the necessary information for function calls within a program and contains return addresses, parameters, local variables, and previous base pointers. 
	
4. What would you find in a data section?

	 • The data section contains initialized global and static variables used to retain values throughout the program's execution.
	 
5. What is the heap used for?

	• The heap is used for dynamic memory allocation and is used to allocate large blocks of memory needed for large arrays or structures for variables and data structures that have varying sizes that can't be determined at run time. 
	
6. What is in the code section of a program's virtual memory space?

	• The code section contains the executable instructions of a program, this is where the compiled machine code is stored and is executed by the CPU.
	
7. What does the `inc` instruction do, and how many operands does it take?

	• the inc - short for increment - instruction increases the value of its operand by 1.
	
8. If I perform a `div` instruction, where would I find the remainder of the binary division (modulo)?

	• The remainder will be stored in a register depending on the bit-size operation it will be dx, edx, or rdx.
	
9. How does `jz` decide whether to jump or not?

	• The jump to zero instruction checks for a zero flag in the flag register and if it is set jz jumps to a specified address. 
	
10. How does `jne` decide whether to jump or not?

	• The jump if not equal instruction checks the zero flag and if it is clear or not set, it jumps to a specified address. 
	
11. What does a `mov` instruction do?

	• The mov instruction copies the value from the source operand to the destination operand leaving the source unmodified.
	 
12. What does the `TF` flag do, and why is it useful for debugging?

	• The trap flag enables single-step mode used for debugging. When it is set, it generates an interrupt after executing each instruction and allows for a debugger to step through a program one instruction at a time. 
	
13. Why would an attacker want to control the RIP register inside a program they want to take control of?

	• By controlling RIP or the instruction pointer, an attacker can execute malicious code and compromise a system. It divert's the program's flow and executes their own code.
	
14. What is the `ax` register and how does it relate to `rax`?

	• ax is the lower 16 bits of the rax register. 
	
15. What is the result of the instruction `xor rax, rax` and where is it stored?

	• This instruction performs a bitwise XOR operation on rax with itself and results in zero with the result stored in rax
	
16. What does the `leave` instruction do in terms of registers to leave a stack frame?

	• The leave instruction is used to exit a stack frame, it moves the base pointer rbp into the stack pointer rsp and then pops the old base pointer from the stack.
	
17. What `pop` instruction is `retn` equivalent to?

	• The retn instruction is equal to the pop instruction, when executed it pops the return address off the stack and jumps to it. 
	
18. What is a stack overflow?

	• A stack overflow occurs when a program writes more data to the stack than the stack can hold this will crash the program and can be a security vulnerability. 
	
19. What is a segmentation fault (a.k.a. a segfault)?

	• A segfault often occurs when a program is trying to read or write to a invalid memory location. It is an error caused by accessing memory that is out of bounds. 
	
20. What are the RSI and RDI registers for that gives them their name?

	• The RSI and RDI, source and destination indices are used in string and array operations and they point to and move data within those operations.

# Ghidra and IDA Screenshots
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/67ae2099-1969-4262-bc5a-548024a41434)
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/acd45aa3-4928-4133-8b44-6a76f68ef45d)

# Solution Explanation
When first examining the crackme I first changed to undefined8 function to be the int main function. This made the rest of the code simpler to understand.

The next step was detecting variables and changing the name to be able to better interpert them. For example, there were 2 variables named local_10 and local_14. I detected these as being the password (local_10) to compare with the user input (local_14). 

This is how it looks after those modifications:
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/3152ea4c-5c06-411e-84e2-6225601743f0)

Following that, I next focused on the validate_key function. The function was simple and I was able to understand that it takes a integer 'param_1' (user input) and checks if it is divisable by hex value 0x4C7 or 1223 and returns true if 'param_1' is a multiple of 1223 and false otherwise. 
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/4caf06c7-aed2-403d-a6f5-baaaaa2af5bf)

As we can see here, this was the correct password. However, we can also see that entering any char will produce the same results.
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/c49e9723-c5e0-4cde-aa65-33d65ad84119)


# Ghidra VS. IDA preferences

In my experience using both IDA and Ghidra, I prefer Ghidra over IDA becuase of the ease of visability of information. To me the user interface was simple and easy to navigate in Ghidra espeacially for new unexperienced users. IDA had a lot of options, which may be useful in future cases and once a user adapts and learns the interface.

I did like the graphical view that shows the flow of the program in IDA 
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/05d38700-8461-4adb-8770-cd62eb8932f0)
