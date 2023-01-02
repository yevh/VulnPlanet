# XML external entity attack (XXE)

The code below is vulnureable to XXE

```
const xml2js = require('xml2js');
const parser = new xml2js.Parser();

// Parse the user-provided XML data
parser.parseString(req.body.xmlData, (err, result) => {
  if (err) {
    // Handle the error
  } else {
    // Do something with the parsed data
    console.log(result);
  }
});
```

# Why it's vulnerable?
This code uses the xml2js library to parse XML data provided by the user in an HTTP request. However, if the XML data contains an external entity declaration, it can be used to read sensitive files on the server or make network requests to other servers.

# Impact?
For example, if the user supplies the following XML data in the request:

```
<?xml version="1.0"?>
<!DOCTYPE data [
  <!ENTITY file SYSTEM "file:///etc/passwd">
]>
<data>
  <secret>&file;</secret>
</data>
```

The xml2js parser will try to read the /etc/passwd file on the server and include its contents in the secret element of the parsed data. This could potentially expose sensitive information, such as user account details, to the attacker.

# How to fix?

To prevent this type of attack, it's important to properly configure the xml2js parser to disable external entity processing. This can be done by setting the xml2js options to include the explicitCharkey: true and explicitArray: false flags:

```
const xml2js = require('xml2js');
const parser = new xml2js.Parser({
  explicitCharkey: true,
  explicitArray: false
});

// Parse the user-provided XML data
parser.parseString(req.body.xmlData, (err, result) => {
  if (err) {
    // Handle the error
  } else {
    // Do something with the parsed data
    console.log(result);
  }
});
```

With these options set, the xml2js parser will treat external entity declarations as ordinary character data, rather than trying to resolve them, which will prevent XXE attacks.
