# Directory traversal

The code below is vulnureable to Directory traversal


```
# Get the user-supplied file path
user_file_path = request.get("file_path")

# Open the file for reading
with open(user_file_path, "r") as f:
  contents = f.read()

return contents
```

# Why it's vulnerable?
In this example, the code takes a user-supplied file path and attempts to open the file for reading. However, this code is vulnerable to a directory traversal attack because it doesn't properly validate the user-supplied file path. 

# Impact?
An attacker could exploit this vulnerability by providing a file path that references a file outside of the intended directory structure, such as ../../secret_data/password.txt.

# How to fix?
To prevent this type of attack, the code should include checks to ensure that the user-supplied file path is within the intended directory structure. This can be done by using a whitelist of allowed paths, or by using a regular expression to match only valid paths. For example:

```
# Define a whitelist of allowed paths
ALLOWED_PATHS = [
  "/var/www/public_html/",
  "/var/www/uploads/",
]

# Get the user-supplied file path
user_file_path = request.get("file_path")

# Check if the file path is in the whitelist
if not any(user_file_path.startswith(path) for path in ALLOWED_PATHS):
  return "Error: Invalid file path"

# Open the file for reading
with open(user_file_path, "r") as f:
  contents = f.read()

return contents
```

In this modified code example, the file path is checked against a whitelist of allowed paths before it is used to open the file. This helps to prevent directory traversal attacks by ensuring that only files within the intended directory structure can be accessed.
