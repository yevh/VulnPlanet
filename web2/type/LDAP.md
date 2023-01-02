# LDAP Injection

The code below is vulnureable to LDAP Injection

```
const ldap = require('ldapjs');
const client = ldap.createClient({
  url: 'ldap://ldap.example.com'
});

// Bind to the LDAP server using the provided username and password
client.bind(req.body.username, req.body.password, (err) => {
  if (err) {
    // Handle the error
  } else {
    // The user has successfully authenticated
  }
});
```


# Why it's vulnerable?
This code uses the ldapjs library to bind to an LDAP server using a username and password provided by the user in an HTTP request. However, if the user supplies a malicious username or password, they can bypass authentication and gain access to the LDAP server.

# Impact?
For example, if the user supplies the following username and password in the request:

```
username: '*)(uid=*))(|(uid=*)))(|(uid=*'
password: 'password'
```

The ldapjs client will construct the following LDAP filter when binding to the server:

```
(uid=*)(uid=*)(|(uid=*)))(|(uid=*)
```

This filter will match any user in the LDAP directory, allowing the attacker to authenticate without providing a valid username and password.


# How to fix?

To prevent this type of attack, it's important to properly validate and sanitize user-provided input before using it in an LDAP query. In this case, you could use a regular expression to ensure that the username only contains allowed characters, such as letters, numbers, and underscores:

```
const ldap = require('ldapjs');
const client = ldap.createClient({
  url: 'ldap://ldap.example.com'
});

// Validate the username using a regular expression
const usernameRegex = /^[a-zA-Z0-9_]+$/;
if (!usernameRegex.test(req.body.username)) {
  // The username is invalid, return an error
  return res.status(400).send('Invalid username');
}

// Bind to the LDAP server using the provided username and password
client.bind(req.body.username, req.body.password, (err) => {
  if (err) {
    // Handle the error
  } else {
    // The user has successfully authenticated
  }
});
```

This code uses a regular expression to validate the username before using it in the LDAP bind operation. If the username contains any characters that are not allowed by the regular expression, the request will be rejected with a bad request error. This will prevent LDAP injection attacks by ensuring that the username can't be used to construct a malicious LDAP filter.
