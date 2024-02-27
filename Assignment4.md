# Crackme1

- For the first crackme the password was easy to discover. There are multiple ways to do so. First method is to run the file with strings. Strings will be able to show the password is : "picklecucumberl337". Running the file in linux and passing the password verifies this is the password.
- Doing further research into the file using IDA, I was also able to see this string in the hex viewer and looking into the functions folder there is a Labels folder and in there we can see FIRST_PASSWORD. This is the same constant string that is expected by the user input variable "local_20 "
- Since the arguement is comparing local_20 with the constant string "picklecucumberl337" this will always be the password and no keygen is required

# Crackme2

- The second crackme was very similar if not identical to the first. It was again expecting a constant string "FIRST_PASSWORD" to compare with user input "local_20"
- The password can be found using the same methods from the first crackme
- The password is "artificialtree"
- No keygen required since it is the same password.
