# Poor Code Quality

The code examples below are vulnureable to Poor Code Quality

Coverage:

- iOS
- Android

# iOS

Buffer overflow and a memory leak

```swift
import Foundation

class User {
  var username: String
  var password: String

  init(username: String, password: String) {
    self.username = username
    self.password = password
  }
}

class UserManager {
  // Store a list of users.
  var users: [User] = []

  // Add a new user to the list.
  func addUser(username: String, password: String) {
    // Check that the username and password are not too long.
    if username.count > 8 || password.count > 8 {
      return
    }

    users.append(User(username: username, password: password))
  }

  // Check if the given username and password match a user in the list.
  func checkCredentials(username: String, password: String) -> Bool {
    // Check if the username and password match any of the users in the list.
    for user in users {
      if user.username == username && user.password == password {
        // Release the user object to avoid a memory leak.
        user = nil
        return true
      }
    }
    return false
  }
}
```

# Why it's vulnerable?
In this app, the ```addUser()``` method checks that the username and password are not too long, but it does not properly release the User object after it is no longer needed, causing a memory leak. Additionally, the ```checkCredentials()``` method allows an attacker to create a username or password with a long string that exceeds the buffer size, causing a buffer overflow.

# Impact?
In the worst scenario, it can lead to code execution.

# How to fix?
To prevent these vulnerabilities, we can improve the code quality of the app by properly releasing the User object after it is no longer needed, and by checking the length of both the username and password before adding them to the list of users. Here is an updated version of the app that includes these changes:

```swift
import Foundation

class User {
  var username: String
  var password: String

  init(username: String, password: String) {
    self.username = username
    self.password = password
  }
}

class UserManager {
  // Store a list of users.
  var users: [User] = []

  // Add a new user to the list.
  func addUser(username: String, password: String) {
    // Check that the username and password are not too long.
    if username.count > 8 || password.count > 8 {
      return
    }

    users.append(User(username: username, password: password))
  }

  // Check if the given username and password match a user in the list.
  func checkCredentials(username: String, password: String) -> Bool {
    // Check if the username and password match any of the users in the list.
    for user in users {
      if user.username == username && user.password == password {
        // Release the user object to avoid a memory leak.
        users = users.filter { $0 !== user }
        return true
      }
    }
    return false
  }
}
```
In this updated version of the app, the ```checkCredentials()``` method filters the list of users to remove the matched user, instead of setting the user object to nil. This avoids a memory leak and allows the User object to be deallocated properly. Additionally, the ```addUser()``` and ```checkCredentials()``` methods both check the length of the username and password before adding them to the list of users, which prevents buffer overflows.

# Android

Buffer overflow and a memory leak

```kotlin
import android.content.Context;
import android.util.Log;

import java.util.ArrayList;
import java.util.List;

public class UserManager {
  private static final String TAG = "UserManager";

  // Store a list of users.
  private List<User> users = new ArrayList<>();

  // Add a new user to the list.
  public void addUser(String username, String password) {
    // Check that the username and password are not too long.
    if (username.length() > 8 || password.length() > 8) {
      return;
    }

    users.add(new User(username, password));
  }

  // Check if the given username and password match a user in the list.
  public boolean checkCredentials(String username, String password) {
    // Check if the username and password match any of the users in the list.
    for (User user : users) {
      if (user.getUsername().equals(username) && user.getPassword().equals(password)) {
        // Release the user object to avoid a memory leak.
        user = null;
        return true;
      }
    }
    return false;
  }
}

class User {
  private String username;
  private String password;

  public User(String username, String password) {
    this.username = username;
    this.password = password;
  }

  public String getUsername() {
    return username;
  }

  public String getPassword() {
    return password;
  }
}
```

# Why it's vulnerable?
n this app, the ```addUser()``` method checks that the username and password are not too long, but it does not properly release the User object after it is no longer needed, causing a memory leak. Additionally, the ```checkCredentials()``` method allows an attacker to create a username or password with a long string that exceeds the buffer size, causing a buffer overflow.

# Impact?
In the worst scenario, it can lead to code execution.

# How to fix?
To prevent these vulnerabilities, we can improve the code quality of the app by properly releasing the User object after it is no longer needed, and by checking the length of both the username and password before adding them to the list of users. Here is an updated version of the app that includes these changes:

```kotlin
import android.content.Context;
import android.util.Log;

import java.util.ArrayList;
import java.util.List;

public class UserManager {
  private static final String TAG = "UserManager";

  // Store a list of users.
  private List<User> users = new ArrayList<>();

  // Add a new user to the list.
  public void addUser(String username, String password) {
    // Check that the username and password are not too long.
    if (username.length() > 8 || password.length() > 8) {
      return;
    }

    users.add(new User(username, password));
  }

  // Check if the given username and password match a user in the list.
  public boolean checkCredentials(String username, String password) {
    // Check if the username and password match any of the users in the list.
    for (int i = 0; i < users.size(); i++) {
      User user = users.get(i);
      if (user.getUsername().equals(username) && user.getPassword().equals(password)) {
        // Release the user object to avoid a memory leak.
        users.remove(i);
        return true;
      }
    }
    return false;
  }
}

class User {
  private String username;
  private String password;

  public User(String username, String password) {
    this.username = username;
    this.password = password;
  }

  public String getUsername() {
    return username;
  }

  public String getPassword() {
    return password;
  }
}
```

In this updated version of the app, the ```checkCredentials()``` method removes the matched user from the list of users, instead of setting the user object to null. This avoids a memory leak and allows the User object to be deallocated properly. Additionally, the ```addUser()``` and ```checkCredentials()``` methods both check the length of the username and password before adding them to the list of users, which prevents buffer overflows.
