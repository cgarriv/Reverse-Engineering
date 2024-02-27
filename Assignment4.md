# Crackme1

- For the first crackme the password was easy to discover. There are multiple ways to do so. First method is to run the file with strings. Strings will be able to show the password is : "picklecucumberl337". Running the file in linux and passing the password verifies this is the password.
- Doing further research into the file using IDA, I was also able to see this string in the hex viewer and looking into the functions folder there is a Labels folder and in there we can see FIRST_PASSWORD. This is the same constant string that is expected by the user input variable "local_20 "
- Since the arguement is comparing local_20 with the constant string "picklecucumberl337" this will always be the password and no keygen is required

![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/bade1fc6-1742-40ef-a7c8-eaf96665e25c)


# Crackme2

- The second crackme was very similar if not identical to the first. It was again expecting a constant string "FIRST_PASSWORD" to compare with user input "local_20"
- The password can be found using the same methods from the first crackme
- The password is "artificialtree"
- No keygen required since it is a constant password.

![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/b0056dfc-c520-4ceb-98dd-9bef30777b50)
![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/0c7484e7-b038-461b-a9e7-b15862b17603)


# Crackme3

- This crackme required a bit more work to understand what it required as the correct password. While true, you can use the same methods from above to be able to see the password, you still need to understand what the code is accepting or rejecting as the correct user input.
- There are two variables "iVar1" and "iVar2" that are used to compare the results of the input. 'local_48' is a character pointer that is used to store the user's input.
- The key difference in this crackme is the use of two constant strings FIRST_PASSWORD1 and FIRST_PASSWORD2, which can be indicative that the password is a concatenation.
- the variables used to check the user input iVar1 and iVar2 are used to compare local_48 our user input pointer with FIRST_PASSWORD2 and iVar2 checks to see if FIRST_PASSWORD1 and FIRST_PASSWORD2 are not only correct but also are concatenated corretly.
- Correct password = "strawberrykiwi"
- No keygen required since it is a constant password

![image](https://github.com/cgarriv/Reverse-Engineering/assets/122755821/05081439-6820-4dbd-b793-c039582ae968)


# Control Flow 1

- Main function:
- Required password to be 16 characters in length
- calls rock()

- Rock function:
- checks the character at index 3
- if "2" it calls paper()
- if outside the range it exits

- Paper function:
- Checks index 7

- Scissors function:
- Checks index 1
- If it is '6' it calls spock()

- Spock function:
- checks index 15, if '*' calls win()

- From the functions analysis what is deduced is that:
- The password must be 16 characters in length
- 1st character must = A
- 2nd character must = 6
- 4th character must = 2
- 8th character must = %
- 16th character must = *

### Keygen code:
```
import random
import string

def generate_password():
    password = [random.choice(string.ascii_letters + string.digits + string.punctuation) for _ in range(16)]

    password[0] = 'A'  # 1st character
    password[1] = '6'  # 2nd character
    password[3] = '2'  # 4th character
    password[7] = '%'  # 8th character
    password[15] = '*' # 16th character

    return ''.join(password)

# display the password
print("Generated password:", generate_password())
```

Keygen was verified for functionality

Keygen output examples:

- A6W23F!%YN2"kj(*
- A6~28ot%@%67a25*
- A6Y26ea%/M1q's%*
- A6O2g1-%{8YBroa*
- A6\2Y8Y%^=duFw5*
