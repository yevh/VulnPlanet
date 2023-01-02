# Mass Assignment

The code below is vulnureable to Mass Assignment

```javascript
// This code is for an API endpoint that allows users to create new accounts

// Read the user's input from the request

let username = req.body.username;
let password = req.body.password;
let role = req.body.role;

// Create the new user in the database

User.create({
  username: username,
  password: password,
  role: role
}, (err, user) => {
  if (err) {
    res.status(500).json({
      error: err
    });
    return;
  }

  // Return the new user
  res.json({
    user: user
  });
});
```

# Why it's vulnerable?
The code is allowing users to create new accounts by reading the input from the request and creating the new user in the database. However, this allows the user to potentially set any value for the role field, including the value for the SuperAdmin role, which should not be set by regular users.

# Impact?
This is an example of mass assignment, because the code is not properly limiting the values that can be set for the role field.

# How to fix?
To avoid this type of vulnerability, it is important to implement safeguards that prevent mass assignment. In this case, the code could be updated to only allow the user to set a specific set of values for the role field, rather than allowing them to set any value. This could be done by manually specifying the allowed
