# A04 Insecure Design.md

Insecure design is a broad category representing different weaknesses, expressed as “missing or ineffective control design.” Insecure design is not the source for all other Top 10 risk categories. There is a difference between insecure design and insecure implementation. We differentiate between design flaws and implementation defects for a reason, they have different root causes and remediation. A secure design can still have implementation defects leading to vulnerabilities that may be exploited. An insecure design cannot be fixed by a perfect implementation as by definition, needed security controls were never created to defend against specific attacks. One of the factors that contribute to insecure design is the lack of business risk profiling inherent in the software or system being developed, and thus the failure to determine what level of security design is required.

- **Coverage** 
     - JS
     - Java
     - Python

# JS

## Vulnureable code example

Node.js (JavaScript) does not provide advanced forms of object serialization, yet the JSON (JavaScript Object Notation) format is often used to convert JavaScript data object from/to a string representation. Before the relative recent addition of the JSON.parse method to ECMAScript, developers used to deserialize objects using the eval function. 

```
function myJSONParse(data) {
    return eval(`(${data})`);
}
```

## Prevention code example

Serialize and deserialize JavaScript objects is to use the provided JSON global object.

```
const object = {foo: 123};
JSON.stringify(object) // '{"foo":123}'
JSON.parse('{"foo":123}') // { foo: 123 }
```

# Java

## Vulnureable code example

Spring controller uses the data coming from the client request to deserialize an object.

```
@Controller
public class MyController {
    @RequestMapping(value = "/", method = GET)
    public String index(@CookieValue(value = "myCookie") String myCookieString) {
        // decode the Base64 cookie value
        byte[] myCookieBytes = Base64.getDecoder().decode(myCookieString);

        // use those bytes to deserialize an object
        ByteArrayInputStream buffer = new ByteArrayInputStream(myCookieBytes);
        try (ObjectInputStream stream = new ObjectInputStream(buffer)) {
            MyObject myObject = stream.readObject();

            // ...
        }
    }
}
```

## Prevention code example

Since Java version 9, it has been possible to specify a deserialization filter in several ways. One example is to use the setObjectInputFilter method for ObjectInputStream objects before their use. The setObjectInputFilter method takes, as an argument, a method that implements the filtering logic. The following filter only allows one to deserialize instances of the MyClass class

```
public class MyFilter {
    static ObjectInputFilter.Status myFilter(ObjectInputFilter.FilterInfo info) {
        Class<?> serialClass = info.serialClass();
        if (serialClass != null) {
            return serialClass.getName().equals(MyClass.class.getName())
                    ? ObjectInputFilter.Status.ALLOWED
                    : ObjectInputFilter.Status.REJECTED;
        }
        return ObjectInputFilter.Status.UNDECIDED;
    }
}

ObjectInputStream objectInputStream = new ObjectInputStream(buffer);
stream.setObjectInputFilter(MyFilter::myFilter);
```

# Python

## Vulnureable code example

Flask endpoint provides an example where untrusted data is fed into the pickle.loads function

```
from flask import request
import pickle
import yaml

@app.route('/pickle')
def pickle_loads():
    file = request.files['pickle']
    pickle.load(file) # Noncompliant; Never use pickle module to deserialize user inputs

@app.route('/yaml')
def yaml_load():
    data = request.GET.get("data")
    yaml.load(data, Loader=yaml.Loader) # Noncompliant; Avoid using yaml.load with unsafe yaml.Loader
```

## Prevention code example

```
from flask import request
import yaml

@app.route('/yaml')
def yaml_load():
    data = request.GET.get("data")
    yaml.load(data) # Compliant;  Prefer using yaml.load with the default safe loader
```
