# Prototype pullation

The code below is vulnureable to Prototype pullation

```
const user = {
  username: 'johndoe',
  password: 'password123'
};

// Merge the user data with the user-provided data
Object.assign(user, req.body.data);

// The user object now contains the user-provided data,
// including any properties that were added to the prototype
console.log(user);
```

# Why it's vulnerable?
This code uses the Object.assign() method to merge an object containing user data with a user-provided object. However, if the user supplies an object that modifies the prototype of a built-in object, it will pollute the prototype and cause unexpected behavior in the code.

# Impact?
For example, if the user supplies the following object in the request:

```
data: {
  __proto__: {
    toString: () => 'hacked'
  }
}
```

The user object will be modified as follows:

```
user = {
  username: 'johndoe',
  password: 'password123',
  __proto__: {
    toString: () => 'hacked'
  }
}
```

Now, any code that uses the toString() method on the user object will be affected by the prototype pollution. For example, calling console.log(user.toString()) will print 'hacked' instead of the expected output.

# How to fix?

To prevent this type of attack, it's important to properly validate and sanitize user-provided input before using it to modify an object. In this case, you could use the Object.getPrototypeOf() method to check if the user-provided object has a prototype, and only merge it with the user object if it doesn't:

```
const user = {
  username: 'johndoe',
  password: 'password123'
};

// Check if the user-provided object has a prototype
if (Object.getPrototypeOf(req.body.data) === Object.prototype) {
  // Merge the user data with the user-provided data
  Object.assign(user, req.body.data);
} else
```
