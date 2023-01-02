# Insecure Deserialization

The code below is vulnureable to Insecure Deserialization

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;

public class InsecureDeserialization {
  public static void main(String[] args) throws IOException, ClassNotFoundException {
    // Create a malicious object
    Object payload = new EvilObject();

    // Write the object to a byte array
    byte[] bytes = toByteArray(payload);

    // Read the object from the byte array
    Object object = fromByteArray(bytes);

    // Use the object
    System.out.println(object);
  }

  private static byte[] toByteArray(Object object) throws IOException {
    // Use an ObjectOutputStream to write the object to a byte array
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    ObjectOutputStream oos = new ObjectOutputStream(baos);
    oos.writeObject(object);
    oos.close();
    return baos.toByteArray();
  }

  private static Object fromByteArray(byte[] bytes) throws IOException, ClassNotFoundException {
    // Use an ObjectInputStream to read the object from a byte array
    ByteArrayInputStream bais = new ByteArrayInputStream(bytes);
    ObjectInputStream ois = new ObjectInputStream(bais);
    Object object = ois.readObject();
    ois.close();
    return object;
  }

  static class EvilObject implements Serializable {
    // The evil object contains code that will be executed when the object is deserialized
    private void readObject(ObjectInputStream stream) throws IOException, ClassNotFoundException {
      stream.defaultReadObject();
      System.out.println("Insecure deserialization vulnerability!");
      System.exit(1);
    }
  }
}
```

# Why it's vulnerable?
This code defines a MaliciousObject class that implements the Serializable interface. The class contains a readObject() method that will be executed when the object is deserialized. 

# Impact?
The attacker could create an instance of the MaliciousObject class and serialize it to a byte array:

```java
MaliciousObject payload = new MaliciousObject();
byte[] bytes = toByteArray(payload);
```
The attacker could then send the serialized object to the victim, who would deserialize it and execute the malicious code:

```java
Object object = fromByteArray(bytes);
```

When the victim deserializes the object, the code in the readObject() method will be executed, printing the message and exiting the program. This could allow the attacker to gain unauthorized access to the victim's system or perform other malicious actions.

# How to fix?
To prevent this type of attack, it's important to properly validate and sanitize user-provided input before deserializing it. This could involve using a whitelist of classes that are allowed to be deserialized. For example:

```java
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.util.Arrays;
import java.util.List;

public class SecureDeserialization {
  public static void main(String[] args) throws IOException, ClassNotFoundException {
    // Create a list of allowed classes
    List<Class<?>> allowedClasses = Arrays.asList(String.class, Integer.class);

    // Create a malicious object
    Object payload = new EvilObject();

    // Write the object to a byte array
    byte[] bytes = toByteArray(payload);

    // Read the object from the byte array
    Object object = fromByteArray(bytes, allowedClasses);

    // Use the object
    System.out.println(object);
  }

  private static byte[] toByteArray(Object object) throws IOException {
    // Use an ObjectOutputStream to write the object to a byte array
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    ObjectOutputStream oos = new ObjectOutputStream(baos);
    oos.writeObject(object);
    oos.close();
    return baos.toByteArray();
  }

  private static Object fromByteArray(byte[] bytes, List<Class<?>> allowedClasses) throws IOException, ClassNotFoundException {
    // Use an ObjectInputStream to read the object from a byte array
    ByteArrayInputStream bais = new ByteArrayInputStream(bytes);
    ObjectInputStream ois = new ObjectInputStream(bais) {
      // Override the resolveClass() method to check the class against the whitelist
      protected Class<?> resolveClass(ObjectStreamClass desc) throws IOException, ClassNotFoundException {
        Class<?> clazz = super.resolveClass(desc);
        if (!allowedClasses.contains(clazz)) {
          throw new InvalidClassException("Unauthorized class: " + clazz.getName());
        }
        return clazz;
      }
    };
    Object object = ois.readObject();
    ois.close();
    return object;
  }
```
