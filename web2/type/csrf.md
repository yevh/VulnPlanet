# Cross Site Request Forgery (CSRF)

The code below is vulnureable to Cross Site Request Forgery (CSRF)

```
const express = require('express');
const app = express();

// Set up the route for the homepage
app.get('/', (req, res) => {
  // Check if the user is authenticated
  if (req.session.authenticated) {
    // The user is authenticated, render the page
    res.render('index');
  } else {
    // The user is not authenticated, redirect to the login page
    res.redirect('/login');
  }
});

// Set up the route for the login page
app.get('/login', (req, res) => {
  // Render the login form
  res.render('login');
});

// Set up the route for the login action
app.post('/login', (req, res) => {
  // Get the username and password from the request body
  const { username, password } = req.body;

  // Authenticate the user
  if (authenticateUser(username, password)) {
    // The user is authenticated, set the session flag and redirect to the homepage
    req.session.authenticated = true;
    res.redirect('/');
  } else {
    // The user is not authenticated, show an error message
    res.render('login', {
      error: 'Invalid username or password'
    });
  }
});

// Start the server on port 3000
app.listen(3000);
```

# Why it's vulnerable?
This code sets up an Express app with routes for the homepage, login page, and login action. The login action authenticates the user and sets a flag in the session to indicate that the user is authenticated. However, if an attacker can trick a logged-in user into visiting a malicious website, they can use a CSRF attack to perform unauthorized actions on the victim's behalf.

# Impact?
For example, if the attacker creates a page with the following HTML and JavaScript:

```
<form method="POST" action="http://localhost:3000/login">
  <input type="hidden" name="username" value="attacker">
  <input type="hidden" name="password" value="password123">
  <input type="submit" value="Submit">
</form>
<script>
  // Submit the form automatically when the page loads
  document.forms[0].submit();
</script>
```

And then tricks a logged-in user into visiting the page, the form will be automatically submitted and the attacker's username and password will be sent to the /login route on the victim's server. If the attacker's credentials are valid, the login action will authenticate the attacker and set the session flag, allowing the attacker to perform actions.

# How to fix?

The code below sets up an Express app with routes for the homepage and login action. The homepage route generates a unique token for the user's session and stores it in the user's session. The token is then passed to the homepage template as a local variable.

```
const express = require('express');
const app = express();

// Set up the route for the homepage
app.get('/', (req, res) => {
  // Generate a unique token for the user's session
  const csrfToken = generateToken();

  // Store the token in the user's session
  req.session.csrfToken = csrfToken;

  // Render the homepage, passing the token as a local variable
  res.render('index', { csrfToken });
});

// Set up the route for the login action
app.post('/login', (req, res) => {
  // Get the username and password from the request body
  const { username, password } = req.body;

  // Get the CSRF token from the request body
  const csrfToken = req.body.csrfToken;

  // Check if the CSRF token in the request matches the token in the user's session
  if (csrfToken === req.session.csrfToken) {
    // The tokens match, authenticate the user
    if (authenticateUser(username, password)) {
      // The user is authenticated, set the session flag and redirect to the homepage
      req.session.authenticated = true;
      res.redirect('/');
    } else {
      // The user is not authenticated, show an error message
      res.render('login', {
        error: 'Invalid username or password'
      });
    }
  } else {
    // The tokens don't match, return an error
    res.status(400).send('Invalid CSRF token');
  }
});

// Start the server on port 3000
app.listen(3000);
```
