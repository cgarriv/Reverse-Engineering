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

