# Assignment 6: More Advanced Crackmes

### Decryptme#1 (Crackme4)





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
