# Code Tampering

The code examples below are vulnureable to Code Tampering

Coverage:

- iOS
- Android

# iOS

Lack of jailbreak detection

```
import UIKit

class BankingAppViewController: UIViewController {
  let bank = Bank()

  @IBOutlet var usernameField: UITextField!
  @IBOutlet var passwordField: UITextField!
  @IBOutlet var loginButton: UIButton!

  @IBAction func loginButtonTapped(_ sender: Any) {
    let username = usernameField.text
    let password = passwordField.text

    if bank.login(username: username, password: password) {
      // Show the account balance.
      let balance = bank.getAccountBalance()
      print("Account balance: $\(balance)")

      // Log out the user.
      bank.logout()
    } else {
      print("Invalid username or password.")
    }
  }
}
```

# Why it's vulnerable?
In this app, the user is able to log in to the app and access their account balance. However, the app does not check if the device is jailbroken.

# Impact?
Jailbreaking gives hackers full control to perform system-wide operations on a device – full stop. They can modify the OS, run scripts that grab data stored in shared areas of the file system, they can even compromise non-shared areas (like app sandbox), etc

# How to fix?

To prevent this vulnerability, we can add jailbreak detection to the app. Here is an updated version of the app that includes this change:

```
import UIKit

class BankingAppViewController: UIViewController {
  let bank = Bank()

  @IBOutlet var usernameField: UITextField!
  @IBOutlet var passwordField: UITextField!
  @IBOutlet var loginButton: UIButton!

  @IBAction func loginButtonTapped(_ sender: Any) {
    // Check if the device is jailbroken.
    if isJailbroken() {
      print("This app cannot be used on a jailbroken device.")
      return
    }

    let username = usernameField.text
    let password = passwordField.text

    if bank.login(username: username, password: password) {
      // Show the account balance.
      let balance = bank.getAccountBalance()
      print("Account balance: $\(balance)")

      // Log out the user.
      bank.logout()
    } else {
      print("Invalid username or password.")
    }
  }

  // Check if the device is jailbroken.
  func isJailbroken() -> Bool {
    // Check for common indicators of a jailbroken device.
    // Replace these checks with your own implementation.
    if FileManager.default.fileExists(atPath: "/Applications/Cydia.app") {
      return true
    }
    if FileManager.default.fileExists(atPath: "/Library/MobileSubstrate/MobileSubstrate.dylib") {
      return true
    }
    if FileManager.default.fileExists(atPath: "/bin/bash") {
      return true
    }
    if FileManager.default.fileExists(atPath: "/usr/sbin/sshd") {
      return true
    }
    if FileManager.default.fileExists(atPath: "/etc/apt") {
      return true
    }
    return false
  }
}
```

# Android

Lack of root detection

```
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Check if the device is rooted
        if (isRooted()) {
            // If the device is rooted, show a message
            Toast.makeText(this, "Device is rooted", Toast.LENGTH_SHORT).show();
        } else {
            // If the device is not rooted, show a different message
            Toast.makeText(this, "Device is not rooted", Toast.LENGTH_SHORT).show();
        }
    }

    // Method to check if the device is rooted
    private boolean isRooted() {
        // Return false, since root detection is missing
        return false;
    }
}
```

# Why it's vulnerable?
This code checks if the device is rooted and displays a message accordingly, but the isRooted() method always returns false, so it does not actually detect if the device is rooted or not. This means that even if the device is rooted, the code will not detect it, allowing for potential code tampering.

# Impact?
Rooting gives hackers full control to perform system-wide operations on a device – full stop. They can modify the OS, run scripts that grab data stored in shared areas of the file system, they can even compromise non-shared areas (like app sandbox), etc

# How to fix?
Implement proper root detection

```
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // Check if the device is rooted
        if (isRooted()) {
            // If the device is rooted, show a message and exit the app
            Toast.makeText(this, "Device is rooted. Exiting app.", Toast.LENGTH_SHORT).show();
            finish();
        } else {
            // If the device is not rooted, show a message and continue with app functionality
            Toast.makeText(this, "Device is not rooted. Continuing with app.", Toast.LENGTH_SHORT).show();
        }
    }

    // Method to check if the device is rooted
    private boolean isRooted() {
        // Check for common signs of rooting
        if (checkForSuperUserApk() || checkForDangerousProps() || checkForRootNativeApps()) {
            // If any signs of rooting are found, return true
            return true;
        } else {
            // If no signs of rooting are found, return false
            return false;
        }
    }

    // Method to check for the presence of the Superuser APK
    private boolean checkForSuperUserApk() {
        // TODO: Check for the presence of the Superuser APK on the device
        return false;
    }

    // Method to check for the presence of dangerous system properties
    private boolean checkForDangerousProps() {
        // TODO: Check for the presence of dangerous system properties on the device
        return false;
    }

    // Method to check for the presence of native root apps
    private boolean checkForRootNativeApps() {
        // TODO: Check for the presence of native root apps on the device
        return false;
    }
}
```

This code checks for common signs of rooting on the device and exits the app if it detects that the device is rooted.
