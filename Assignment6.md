# Assignment 6: More Advanced Crackmes

### Decryptme#1 (Crackme4)

Under function FUN_00013424 I noticed a HEX value that kept reappearing in the other functions that where comparing a local variable with  0x105 or decimal value (261). More importantly I noticed that the value was being passed in a return call 1stcpynA that uses the memory stack 0x4 and references a IpString1 or the first argument being passed. In the same variable parameter it is being added with local_121. I found that local_121 was referencing iStack_10 a int value with the arithmetic value of  ((-8) +1)) or -7. 
- I deduced that the 1st argument is getting the value of 0x105 and added by iStack_10 to get a final value of 254. Testing the value in the GUI I received the following message.
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/05abef0e-846e-4e24-a796-66bb9505d890)

- I was also able to see in FUN_00012524 that uVar2 or the second parameter from user input was being set as 0x7f or decimal 127 and in FUN_0001258c uVar2 was being passed as a param_3 check with a mathematical operation of ((uVar2= uVar2 - 1)). Passing the value of 126 also gave me a successful message.
- ![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/3016355f-74f0-432b-ae83-d0ca85b24eb6)

- Further analyzing the code I realized that the 'uVar' values are being added together and checking for validation in this way. So I figured that any number starting at 'uVar2' (126) and adding 'uVar3' or iStack_10 (128) will work so the key is n=126 or any value adding 128 to the first parameter check (e.g. 126(+128).., 254, 382, 510...)

-![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/70c5ddec-a607-4d47-8d66-4d004014f4ce)





### Crackme05_64bit

- Analyzing the crackme in ghidra I used the source to sink method to determine which functions where being called as well as where the source was and where the sink was.
- We can deduce that we must avoid any input that will call the bomb() function
- The main challenge was in deducing the functions 'rock', 'paper', 'scissors', 'cracker' and 'decraycray'.
- Here is the deduced logic from each function with the required conditions that must be met.

#### rock()
- Serial length must = 19 characters
- Characters must not be less than '-' (ASCII 45) or more than 'z'

#### paper()
- XOR operation
	- XOR result at position 10 and 8 add 0x30 must be numeric
	- XOR result at position 13 and 5 add 0x30 must be numeric
- Character matching
	- Character at position 3 and 15 must match first numerical value
	- The 1st character and character at position 18 must not match the second numerical value

#### scissors()
- Sum checks
	- The sum of characters at position 2 and 1, and the sum of characters at positions 17 and 16 must both be greater than 0xAB
	- These 2 sums must not be equal to each other

#### cracker()
- Specified sum
	- The function requires that the sum of the characters at position 14,4, and 9 be equal to 0x87

- After gathering the required conditions from each function I made a keygen that will satisfy each function and avoid calling bomb()

```
import random

  

def generate_serial():

# Baseline 'A'

serial = ['A'] * 19

  

allowed_chars = [chr(i) for i in range(45, 123) if i not in [36, 42, 64]]

  

serial[1], serial[2] = 'm', 'n'

serial[16], serial[17] = 'o', 'p'

serial[14], serial[4], serial[9] = '1', '2', chr(135 - ord('1') - ord('2')) # For 'cracker' condition

  
  

serial[8] = '0'

serial[10] = '1' # Choosing characters that ensure XOR condition is met

serial[5] = '0' # reset

serial[13] = '2' # Ensure XOR yields a different numeric result

  

# Adjust positions 3 and 15 to match XOR result of positions 10 and 8

serial[3] = serial[15] = chr(ord(serial[10]) ^ ord(serial[8]) ^ 0x30)

  

# Ensure positions 0 and 18 do not match XOR result of positions 13 and 5

serial[0] = '3'

serial[18] = '4' # Different from the result for positions 13 and 5

  

# Fill in any remaining 'A' with characters from allowed_chars to respect 'rock' conditions

for i in range(len(serial)):

if serial[i] == 'A':

serial[i] = random.choice(allowed_chars)

  

return ''.join(serial)

  

# Generate serial

serial = generate_serial()

print("Generated Serial:", serial)


```

- After attempting the keygen with the crackme I kept getting a call to bomb() with the ROCK 4: Serial not 19 chars!
- After some modifications I landed on ROCK 1: 9 - $
- I understood that the 9th character was not correct and modified the keygen once more
- Finally I decided to manually change the keygen to read in '$' in this format ``` \" $ \" ```
- I appears the crackme was not accepting the $ as a char and passing it in with the escape values it finally took it as the correct serial.
