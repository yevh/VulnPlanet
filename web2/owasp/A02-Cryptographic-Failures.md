# A02:2021 – Cryptographic Failures

The first thing is to determine the protection needs of data in transit and at rest. For example, passwords, credit card numbers, health records, personal information, and business secrets require extra protection, mainly if that data falls under privacy laws, e.g., EU's General Data Protection Regulation (GDPR), or regulations, e.g., financial data protection such as PCI Data Security Standard (PCI DSS). 

- **Coverage** 
     - JS

# JS

## Vulnureable code example

Using weak hashing algorithms

```
const crypto = require("crypto");

const hash = crypto.createHash('sha1'); // Weak
```

Using weak pseudorandom number generators (PRNGs) 

```
const val = Math.random(); // Weak

// Check if val is used in a security context.
```

## Prevention code example

Safer alternatives, such as SHA-256, SHA-512, SHA-3 are recommended, and for password hashing, it’s even better to use algorithms that do not compute too "quickly", like bcrypt, scrypt, argon2 or pbkdf2 because it slows down brute force attacks.

```
const crypto = require("crypto");

const hash = crypto.createHash('sha512'); // Compliant
```

Use a cryptographically strong pseudorandom number generator

```
// Client side

const crypto = window.crypto || window.msCrypto;
var array = new Uint32Array(1);
crypto.getRandomValues(array); // Compliant for security-sensitive use cases

// Server side

const crypto = require('crypto');
const buf = crypto.randomBytes(1); // Compliant for security-sensitive use cases
```
