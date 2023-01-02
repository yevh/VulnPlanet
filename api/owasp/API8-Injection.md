# API8:2019 Injection

The code below is vulnureable to Injection


```
// This code is for an API endpoint that allows users to search for products by name

// Read the user's input from the request

let productName = req.query.name;

// Build the search query using the user's input

let query = `SELECT * FROM products WHERE name LIKE '%${productName}%'`;

// Execute the search query

Product.find(query, (err, products) => {
  if (err) {
    res.status(500).json({
      error: err
    });
    return;
  }

  // Return the search results
  
  res.json({
    products: products
  });
});
```

# Why it's vulnerable?
The code is allowing users to search for products by name by reading the user's input from the request and building a search query using the user's input. However, this allows the user to potentially inject malicious code into the search query, such as a SQL injection attack.

# Impact?
This is an example of injection, because the code is not properly sanitizing the user's input before using it in the search query.

# How to fix?
To avoid this type of vulnerability, it is important to properly sanitize user input before using it in your code. In this case, the code could be updated to use parameterized queries, which automatically sanitize the user's input and prevent injection attacks. This could be done by using an object-mapping library that supports parameterized queries, such as mongoose for MongoDB, or by using a database driver that supports parameterized queries, such as pg for PostgreSQL. This would ensure that the user's input is properly sanitized and cannot be used to inject malicious code into your search query.
