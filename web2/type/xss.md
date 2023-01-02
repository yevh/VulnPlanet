# Cross-Site Scripting (XSS)

The code below is vulnureable to XSS

```
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  // Get the user-provided message from the query string
  const message = req.query.message;

  // Send a response with the message as the body
  res.send(`
    <html>
      <body>
        Message: ${message}
      </body>
    </html>
  `);
});

// Start the server on port 3000
app.listen(3000);
```

# Why it's vulnerable?
This code sets up an Express app with a single route that displays a user-provided message on the page. However, if the user supplies a message containing JavaScript code, it will be executed on the page when the response is rendered.

# Impact?
For example, if a user sends a request to http://localhost:3000/?message=<script>alert('XSS attack')</script>, the page will show an alert box with the message "XSS attack".

# How to fix?
To prevent this type of attack, it's important to properly sanitize user-provided input to ensure it doesn't contain any malicious code. In this case, you could use the escape function provided by the Express library to escape any special characters in the message before rendering it on the page:

```
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  // Get the user-provided message from the query string
  const message = req.query.message;

  // Escape the message to remove any special characters
  const escapedMessage = express.escape(message);

  // Send a response with the escaped message as the body
  res.send(`
    <html>
      <body>
        Message: ${escapedMessage}
      </body>
    </html>
  `);
});

// Start the server on port 3000
app.listen(3000);
```

Now, if a user tries to include JavaScript code in the message, it will be escaped and treated as a string, rather than being executed as code on the page.
