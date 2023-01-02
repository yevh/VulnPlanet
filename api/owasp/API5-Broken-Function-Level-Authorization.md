# Broken Function Level Authorization

The code below is vulnureable to Broken Function Level Authorization


```javascript
// This code is for an API endpoint that allows users to access and update their profile information

// Read the user's input from the request

let userId = req.body.userId;
let username = req.body.username;
let email = req.body.email;

// Check if the user is authenticated

if (!req.isAuthenticated()) {
  res.status(401).json({
    error: "Unauthorized"
  });
  return;
}

// Check if the user has permission to access their own profile

if (userId !== req.user.id) {
  res.status(403).json({
    error: "Forbidden"
  });
  return;
}

// Update the user's profile information in the database

User.findByIdAndUpdate(userId, {
  username: username,
  email: email
}, (err, user) => {
  if (err) {
    res.status(500).json({
      error: err
    });
    return;
  }

  // Return the updated profile information
  res.json({
    user: user
  });
});
```

# Why it's vulnerable?
The code is checking if the user is authenticated and has permission to access their own profile before allowing the update to be made. However, the code is not checking if the user has permission to update their profile, which means that an attacker who has authenticated as the user could potentially update the profile without the user's permission.

# Impact?
This is an example of broken function level authorization, because the code is not properly verifying that the user has the appropriate permissions to perform the specific function (in this case, updating the user's profile).

# How to fix?
To avoid this type of vulnerability, it is important to implement robust and comprehensive authorization checks that verify the user's permissions for each specific function they are attempting to perform. In this case, the code could be updated to check the user's permissions for the specific action of updating their profile, rather than just checking their authentication and access to their own profile. This would ensure that only users with the appropriate permissions can update their own profile.
