# Broken User Authentication

The code below is vulnureable to Broken User Authentication


```
// This code is for an API endpoint that allows users to login

// Read the user's input from the request

let username = req.body.username;
let password = req.body.password;

// Check if the user's credentials are correct

if (username === "admin" && password === "password") {
  // Set the user as authenticated
  req.isAuthenticated = true;

  res.json({
    success: "Login successful"
  });
} else {
  res.status(401).json({
    error: "Invalid username or password"
  });
}
```

# Why it's vulnerable?
The code is checking if the user's credentials are correct before allowing them to login. However, the code is using hard-coded credentials, which means that any user who knows the username and password can login.

# Impact?
This is an example of broken user authentication, because the code is not properly verifying the user's identity and is allowing anyone who knows the correct credentials to access the system.

# How to fix?
To avoid this type of vulnerability, it is important to implement robust and secure authentication methods that verify the user's identity in a reliable and secure way. In this case, the code could be updated to use a secure authentication scheme, such as password hashing or two-factor authentication, to verify the user's credentials. This would ensure that only users who can provide the correct credentials can access the system.
