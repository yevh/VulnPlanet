# Insufficient Logging & Monitoring

The code below is vulnureable to Insufficient Logging & Monitoring

```
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

// Handle login requests

app.post("/login", (req, res) => {
  // Read the user's input from the request
  let username = req.body.username;
  let password = req.body.password;

  // Check if the username and password are correct
  
  User.findOne({
    username: username,
    password: password
  }, (err, user) => {
    if (err) {
      res.status(500).json({
        error: err
      });
      return;
    }

    if (user) {
      // Return the user if the login is successful
      res.json({
        user: user
      });
    } else {
      res.status(401).json({
        error: "Invalid username or password"
      });
    }
  });
});

// Start the server

const port = process.env.PORT || 8080;
app.listen(port, () => {
  console.log(`API server listening on port ${port}`);
});
```

# Why it's vulnerable?
The API is not logging or monitoring failed login attempts.

# Impact?
This means that if an attacker tries to guess a user's username and password using brute-force, they can do so without being detected or blocked.

# How to fix?
How it can be fixed by implementing logging and monitoring of failed login attempts.
