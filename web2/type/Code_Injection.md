# Code Injection

The code below is vulnureable to Code Injection


```python
# Vulnerable function
def execute_code(code):
  return eval(code)

# User input
user_input = input('Enter some Python code to run: ')

# Execute
result = execute_code(user_input)

print(result)
```

# Why it's vulnerable?
The execute_code function takes a string of Python code as input and evaluates it using the eval function. This is a common mistake as the eval function can execute any code passed to it, which makes the application vulnerable to code injection attacks.

# Impact?
An attacker could enter the following code as input: 

```python
import os
os.system('rm -rf /')
```
This code would be executed by the eval function, which would delete all files on the system.

# How to fix?
To avoid code injection vulnerabilities, it is important to never use the eval function or similar constructs to execute user-supplied code. Instead, you should use safer alternatives such as the exec function or a dedicated code execution engine that can validate and sanitize the input code before executing it.
