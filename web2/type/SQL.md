# SQL Injection

The code below is vulnureable to SQL Injection

```javascript
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'test'
});

// Use the user-provided value as a parameter in the query
const query = `SELECT * FROM users WHERE username = '${req.body.username}'`;
connection.query(query, (err, result) => {
  if (err) {
    // Handle the error
  } else {
    // Do something with the result
    console.log(result);
  }
});
```


# Why it's vulnerable?
This code uses the MySQL Node.js driver to connect to a MySQL database and search for a user with a given username. However, if the user supplies a malicious value for the username parameter, they can modify the query to return arbitrary data from the database.

# Impact?
For example, if the user supplies the following value for the username parameter:

```javascript
username: ' OR 1=1--
```

The SELECT statement will be constructed as follows:

```javascript
SELECT * FROM users WHERE username = '' OR 1=1--'
```

This query will return all rows from the users table, allowing the attacker to retrieve sensitive information from the database.

# How to fix?

To prevent this type of attack, it's important to properly validate and sanitize user-provided input before using it in a SQL query. In this case, you could use the mysql.escape() function to escape special characters in the username parameter:

```javascript
const mysql = require('mysql');
const connection = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'password',
  database: 'test'
});

// Escape the username using the mysql.escape() function
const username = mysql.escape(req.body.username);

// Use the user-provided value as a parameter in the query
const query = `SELECT * FROM users WHERE username = ${username}`;
connection.query(query, (err, result) => {
  if (err) {
    // Handle the error
  } else {
    // Do something with the result
    console.log(result);
  }
});
```

This code uses the mysql.escape() function to escape any special characters in the username parameter before using it in the SELECT statement. This will prevent SQL injection attacks by ensuring that the username parameter can't be used to construct a malicious query.
