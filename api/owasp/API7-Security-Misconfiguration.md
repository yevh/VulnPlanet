# Security Misconfiguration

The code below is vulnureable to Security Misconfiguration

```javascript
// This code is for an API server

const express = require("express");
const app = express();

// Enable CORS

app.use(function(req, res, next) {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});

// Serve static files from the public directory

app.use(express.static("public"));

// Start the server

const port = process.env.PORT || 8080;
app.listen(port, () => {
  console.log(`API server listening on port ${port}`);
});
```

# Why it's vulnerable?
The code is setting up an API server using the express library. The code enables CORS and serves static files from the public directory. However, this configuration allows any client to access the static files in the public directory, which may include sensitive information such as configuration files or secrets. 

# Impact?
This is an example of security misconfiguration, because the server is not properly configured to protect sensitive information from unauthorized access.

# How to fix?
To avoid this type of vulnerability, it is important to properly configure the server and its components to ensure that sensitive information is protected from unauthorized access. In this case, the code could be updated to only allow specific clients to access the static files  in the public directory, rather than allowing any client to access them. Here is an example of how you can prevent security misconfiguration by restricting CORS:

```javascript
// This code is for an API server

const express = require("express");
const app = express();

// Enable CORS, but only for specific trusted clients

app.use(function(req, res, next) {
  let allowedOrigins = ["http://example.com", "http://trusted-client.com"];
  let origin = req.headers.origin;
  if (allowedOrigins.indexOf(origin) > -1) {
    res.header("Access-Control-Allow-Origin", origin);
  }
  res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
  next();
});

// Serve static files from the public directory

app.use(express.static("public"));

// Start the server

const port = process.env.PORT || 8080;
app.listen(port, () => {
  console.log(`API server listening on port ${port}`);
});
```

The code is setting up an API server using the express library. The code enables CORS, but only for specific trusted clients that are listed in the allowedOrigins array.
