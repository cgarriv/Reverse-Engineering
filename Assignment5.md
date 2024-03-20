## Summary:
The objective of this weeks crackmes was to analyze and preform file recovery for encrypted files in a ransomware file.
These crackmes proved to be more difficult and deeper analysis was required to achieve the decryption of the files. 

## Analysis:
In order to successfully decrypt the compromised files, first we open the ransomware in Ghidra.
Practice on previous crackmes and techniques used to crack those proved to be very valuable in solving these ransomwares.
I used the source to sink technique in ghidra and analyzed the program from the sink back to the source. 
Doing this I was able to deduce that some of the functions in the program where of no valuable use while others where the main functions used to decrypt the files. 
I was also able to determine that the key being used was a char'4'. By guessing, I was able to decipher that the encryption method was taking the original file and XORing every character by ^4.
I was also able to determine that the parameters of the main function passed 3 parameters.
In the following screenshots we are able to see a few things.

#### 1. We can see in Ghidra where functions, variables, and hex values are changed to make the program more easy to decipher and understand
#### 2. We can see the linux terminal creating decryption program in python to pass the secert.txt.pay_up file and create a new decrypted file secret.txt. 
We can also see that doing static analyze on the ransomware allowed us to find a string "lumpy_cactus_fruit" that was successful in decrypting the important.txt file but not the secert.txt file.
#### 3. We can see the decryption program "decrypt.py" and how it works.
#### 4. Finally, we can see that the file secret.txt.pay_up was successfully decrypted and saved as secret.txt
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/213dfad5-3905-4a8e-8aea-81a878c04330)
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/60c1dd44-13b2-4352-99ad-8bc5be007819)

I additionally wrote a similar program and tested its functionality, It also was successful in decrypting the secret.txt.pay_up file. It is included below.

```
import sys

def decrypt_file(input_file, output_file, decryption_key):

    with open(input_file, 'rb') as inf, open(output_file, 'wb') as ouf:
        # Read contents of the input file
        encrypted_contents = inf.read()
        
        # Decrypt each byte
        decrypted_contents = bytearray(b ^ decryption_key for b in encrypted_contents)
        
        # Write the decrypted contents to output file
        ouf.write(decrypted_contents)

def main():
    if len(sys.argv) != 3:
        print("Usage: python decrypt.py INFILE OUTFILENAME")
        sys.exit(1)
    
    infile = sys.argv[1]
    outfile = sys.argv[2]
    
    key = ord('4')
    
    # decryption
    decrypt_file(infile, outfile, key)

if __name__ == "__main__":
    main()
```
## Ransomware_2

I had complications when solving ransomware_2. I attempted the same methodology used in the first ransomware but was not successful. I was successful in figuring out the First_Password
being 'delicious' but was not able to decrypt the secret.txt file. Here is my attempt:

#### 1. using Ghidra I analyzed the program using the source to sink technique. I mainly focused on the functions that the variables were calling and looked for the HEX values within those function calls. I thought that those values where the values being passed to compare with the key. Here is my deduced information gathered from this method:

- `FUN_00011220 = 68 or 'D'`
- `FUN_000111a0 = 36 or '$'`
- `FUN_00011210 = 64 or '@'`
- `FUN_00011190 = 32 or ' ' (Space)`
- `FUN_00011160 = 20` (does not correspond directly to a printable ASCII character in the same manner; hex 20 is ' ' (Space))
- `FUN_000111c0 = 44 or ','`
- `FUN_000111b0 = 40 or '('`
- `FUN_000111e0 = 52 or '4'`

- I got the key "$(,4  @D'
- by placing the HEX values in order of the functions as they where called in the ransomware
- I then created a python program that would pass each char value as the key and XOR them as we did with char '4' in ransomware_1 Here is the code:

```
import sys
if (len(sys.argv) != 3):
print("Usage: decrypt.py INFILE OUTFILENAME")
infile = sys.argv[1]
outfile = sys.argv[2]
key_string = "$(,4@D"
key = [ord(c) for c in key_string]
key_length = len(key)
with open(infile, "rb") as inf:
with open(outfile, "wb") as ouf:

        contents = inf.read()
    
        for i, b in enumerate(contents):
            xor_byte = b ^ key[i % key_length]
            ouf.write(xor_byte.to_bytes(1, "big"))
```

- Unfortunately I was unable to decrypt the file using this method and further analysis will be preformed. I will update my work and findings.


- Successful FIRST_PASSWORD discovery
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/71a90790-a635-436a-adb9-5bce1b8b25e5)
- Unsuccessful decryption of secret.txt
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/608aee12-08ce-4570-aa5b-ea19d2b8afc6)




