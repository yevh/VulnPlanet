# Reverse Engineering

The code examples below are vulnureable to Reverse Engineering

Coverage:

- iOS
- Android

# iOS

Enable the debugger on an iOS app

```swift
// Import the necessary modules
import Foundation
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    // Set the window and application delegate properties
    var window: UIWindow?
    var application: UIApplication?

    // Method to enable the debugger
    func enableDebugger() {
        // Check if the debugger is already enabled
        if !isDebuggerAttached() {
            // If the debugger is not enabled, enable it
            let exception = NSException(name: NSExceptionName.internalInconsistencyException, reason: "Debugger enabled", userInfo: nil)
            exception.raise()
        }
    }

    // Method to check if the debugger is attached
    func isDebuggerAttached() -> Bool {
        // Check if the debugger is attached by reading a specific memory address
        var info = kinfo_proc()
        var mib: [Int32] = [CTL_KERN, KERN_PROC, KERN_PROC_PID, getpid()]
        var size = MemoryLayout<kinfo_proc>.stride
        let junk = sysctl(&mib, UInt32(mib.count), &info, &size, nil, 0)
        assert(junk == 0, "sysctl failed")
        return (info.kp_proc.p_flag & P_TRACED) != 0
    }

    // Method to initialize the application
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Enable the debugger
        enableDebugger()

        // Initialize the window and application properties
        self.window = UIWindow(frame: UIScreen.main.bounds)
        self.application = application

        // Set the root view controller and make the window visible
        self.window?.rootViewController = UIViewController()
        self.window?.makeKeyAndVisible()

        return true
    }
}
```

# Why it's vulnerable?
It enables the debugger, which can be used by an attacker to reverse engineer the app. By attaching a debugger to the app, the attacker can step through the app's code, inspect its variables and data, and discover sensitive information, such as hardcoded connectivity strings, encryption keys, and PII data. 

# Impact?
This can allow the attacker to gain access to the app's backend systems and steal sensitive data.

# How to fix?

The isDebuggerAttached() method is used to check if the debugger is attached to the app. If the debugger is detected, the app is terminated immediately.

```swift
// Import the necessary modules
import Foundation
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    // Set the window and application delegate properties
    var window: UIWindow?
    var application: UIApplication?

    // Method to check if the debugger is attached
    func isDebuggerAttached() -> Bool {
        // Check if the debugger is attached by reading a specific memory address
        var info = kinfo_proc()
        var mib: [Int32] = [CTL_KERN, KERN_PROC, KERN_PROC_PID, getpid()]
        var size = MemoryLayout<kinfo_proc>.stride
        let junk = sysctl(&mib, UInt32(mib.count), &info, &size, nil, 0)
        assert(junk == 0, "sysctl failed")
        return (info.kp_proc.p_flag & P_TRACED) != 0
    }

    // Method to initialize the application
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Check if the debugger is attached
        if isDebuggerAttached() {
            // If the debugger is attached, terminate the app
            print("Debugger detected. Exiting.")
            exit(0)
        }

        // Initialize the window and application properties
        self.window = UIWindow(frame: UIScreen.main.bounds)
        self.application = application

        // Set the root view controller and make the window visible
        self.window?.rootViewController = UIViewController()
        self.window?.makeKeyAndVisible()

        return true
    }
}
```

# Android

PII data from an iOS app through can be still through reverse engineering and string table analysis

```kotlin
// Import the necessary modules
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;

// Connect to the database using the hardcoded credentials
String dbUrl = "hardcoded-db-connection-string-with-credentials";
Connection conn = DriverManager.getConnection(dbUrl);

// Query the database for user PII data
Statement stmt = conn.createStatement();
String query = "SELECT * FROM users";
ResultSet result = stmt.executeQuery(query);

// Iterate over the result set and print each row
while (result.next()) {
    int id = result.getInt("id");
    String name = result.getString("name");
    String email = result.getString("email");
    String address = result.getString("address");
    String phone = result.getString("phone");
    System.out.println("ID: " + id + " - Name: " + name + " - Email: " + email + " - Address: " + address + " - Phone: " + phone);
}

// Close the database connection
conn.close();
```

# Why it's vulnerable?
This code uses a hardcoded database connection string that contains authentication credentials to connect to a backend database. It then queries the database for user PII data and iterates over the result set, printing each row of data.

# Impact?
This could potentially be used by an attacker who has discovered the hardcoded connectivity string through string table analysis to gain access to the database and steal PII data about the app's users.

# How to fix?

The ProGuard library is used to obfuscate the strings and variable names. This can make the code even harder to understand and analyze for an attacker, making it more difficult to discover sensitive information, such as the hardcoded database connection string, through string table analysis. 

```kotlin
// Import the necessary modules
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import com.google.proguard.ProGuard;

// Connect to the database using the hardcoded credentials
String a = "hardcoded-db-connection-string-with-credentials";
String b = ProGuard.obfuscate(a);
Connection c = DriverManager.getConnection(b);

// Query the database for user PII data
Statement d = c.createStatement();
String e = "SELECT * FROM users";
String f = ProGuard.obfuscate(e);
ResultSet g = d.executeQuery(f);

// Iterate over the result set and print each row
while (g.next()) {
    int h = g.getInt(ProGuard.obfuscate("id"));
    String i = g.getString(ProGuard.obfuscate("name"));
    String j = g.getString(ProGuard.obfuscate("email"));
    String k = g.getString(ProGuard.obfuscate("address"));
    String l = g.getString(ProGuard.obfuscate("phone"));
    System.out.println("ID: " + h + " - Name: " + i + " - Email: " + j + " - Address: " + k + " - Phone: " + l);
}

// Close the database connection
c.close();
```
