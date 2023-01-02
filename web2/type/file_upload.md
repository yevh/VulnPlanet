# Insecure File upload

The code below is vulnureable to insecure file upload

```php
// Check if the form has been submitted
if (isset($_POST['submit'])) {

    // Get the uploaded file
    $file = $_FILES['file'];

    // Get the file name and extension
    $file_name = $file['name'];
    $file_ext = strtolower(end(explode('.', $file_name)));

    // Set the allowed file types
    $allowed_types = array('jpg', 'jpeg', 'png', 'gif');

    // Check if the uploaded file is allowed
    if (in_array($file_ext, $allowed_types)) {

        // Set the target path for the file
        $target_path = "uploads/" . $file_name;

        // Move the uploaded file to the target path
        move_uploaded_file($file['tmp_name'], $target_path);

        echo "File uploaded successfully!";
    }
    else {
        echo "Error: Only JPG, JPEG, PNG, and GIF files are allowed.";
    }
}
```

# Why it's vulnerable?
This code only checks the file extension to determine if the uploaded file is allowed. This means that a malicious user could potentially upload a file with a malicious payload that has an allowed file extension. 

# Impact?
For example, a user could upload a file named "image.php.png" that contains PHP code that is executed on the server when the file is uploaded. This could allow the attacker to gain unauthorized access to the server or perform other malicious actions. 

# How to fix?
To prevent this type of vulnerability, it is important to not only check the file extension, but also to verify the actual content of the uploaded file to ensure that it is safe.
