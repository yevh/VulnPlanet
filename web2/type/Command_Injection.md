# Command Injection

The code below is vulnureable to Command Injection

```python
def execute_command(cmd):
  import subprocess
  # Run the command and get the output
  output = subprocess.check_output(cmd, shell=True)
  return outputting 

# Get the command to run from the user
cmd = input("Enter the command to run: ")

# Execute the command
output = execute_command(cmd)

print(output)
```

# Why it's vulnerable?
In this code, the ```execute_command``` function takes in a command as an input and runs it using the ```subprocess.check_output``` function. 

# Impact?
This is dangerous because an attacker could potentially inject additional commands into the cmd variable that gets executed. For example, if the attacker inputs ```ls; rm -rf /```, the execute_command function would run the ```ls``` command and then the ```rm -rf / command```, which would delete all the files in the root directory.

# How to fix?
To prevent command injection vulnerabilities, it is important to sanitize user input and only allow certain safe commands to be executed. One way to do this is to use a whitelist of allowed commands and only execute commands that are on the whitelist. Another way is to use a library that provides safe ways to run external commands, such as the shlex module in Python.
