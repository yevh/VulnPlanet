# Improper Assets Management

The code below is vulnureable to Improper Assets Management

```javascript
// This code is for the social network's official API

const express = require("express");
const app = express();

// Connect to the user database

const mongoose = require("mongoose");
mongoose.connect("mongodb://localhost/users");

// Create a user model

const User = mongoose.model("User", {
  username: String,
  password: String,
  name: String,
  email: String,
  phone: String
});

// Implement rate limiting to prevent brute-force attacks

const rateLimiter = require("express-rate-limit");
const limiter = rateLimiter({
  windowMs: 60 * 1000, // 1 minute
  max: 10 // maximum of 10 attempts per minute
});
app.use("/reset-password", limiter);

// Handle reset password requests

app.post("/reset-password", (req, res) => {
  // Read the user's input from the request
  
  let username = req.body.username;
  let token = req.body.token;

  // Check if the token is valid
  
  if (isValidToken(token)) {
    // Update the user's password
    
    User.findOneAndUpdate({
      username: username
    }, {
      password: req.body.newPassword
    }, (err, user) => {
      if (err) {
        res.status(500).json({
          error: err
        });
        return;
      }

      // Return the updated user
      
      res.json({
        user: user
      });
    });
  } else {
    res.status(401).json({
      error: "Invalid token"
    });
  }
});

// Start the server

const port = process.env.PORT || 8080;
app.listen(port, () => {
  console.log(`API server listening on port ${port}`);
});
```

# Why it's vulnerable?
The code is setting up the social network's official API and implementing rate limiting to prevent brute-force attacks. However, the code is not properly managing the API assets, as it is not applying the rate limiting mechanism to the beta API host. 

# Impact?
This allows an attacker to access the beta API and reset the password of any user.

# How to fix?
To fix the issue of improper assets management in the code example above, the rate limiting mechanism should be applied to the beta API host as well.
