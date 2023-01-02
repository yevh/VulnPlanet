# Extraneous Functionality

The code examples below are vulnureable to Extraneous Functionality

Coverage:

- iOS
- Android

# iOS

Hidden interface

```swift
// Import the necessary modules
import Foundation
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    // Set the window and application delegate properties
    var window: UIWindow?
    var application: UIApplication?

    // Method to initialize the application
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Initialize the window and application properties
        self.window = UIWindow(frame: UIScreen.main.bounds)
        self.application = application

        // Set the root view controller and make the window visible
        self.window?.rootViewController = UIViewController()
        self.window?.makeKeyAndVisible()

        return true
    }

    // Method to access the administrative dashboard
    func showAdminDashboard() {
        // TODO: Implement the administrative dashboard
    }
}
```

# Why it's vulnerable?
it includes a hidden interface (the showAdminDashboard() method) that can access the administrative dashboard and back-end API server, but it does not include any code to display the dashboard.

# Impact?
This allows an attacker to discover the administrative REST endpoint URL through string table analysis and use it to execute back-end administrative functionality using curl. This can allow the attacker to access sensitive information and perform unauthorized actions on the backend server.

# How to fix?

In this code, the showAdminDashboard() method is marked as deprecated using the @available attribute. This will prevent the method from being called and will generate a warning if it is used. This makes it less likely for an attacker to discover and exploit the hidden interface. Additionally, the method could be removed altogether to completely eliminate the vulnerability.

```swift
// Import the necessary modules
import Foundation
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    // Set the window and application delegate properties
    var window: UIWindow?
    var application: UIApplication?

    // Method to initialize the application
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Initialize the window and application properties
        self.window = UIWindow(frame: UIScreen.main.bounds)
        self.application = application

        // Set the root view controller and make the window visible
        self.window?.rootViewController = UIViewController()
        self.window?.makeKeyAndVisible()

        return true
    }

    // Method to access the administrative dashboard
    @available(*, deprecated)
    func showAdminDashboard() {
        // TODO: Implement the administrative dashboard
    }
}
```

# Android

Hidden interface

```kotlin
// Import the necessary modules
import android.app.Activity
import android.os.Bundle
import android.widget.Toast

// Define the Activity class
class MainActivity: Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Show a Toast message
        Toast.makeText(this, "Hello, world!", Toast.LENGTH_SHORT).show()
    }

    // Method to access the administrative dashboard
    fun showAdminDashboard() {
        // TODO: Implement the administrative dashboard
    }
}
```

# Why it's vulnerable?
It includes a hidden interface (the showAdminDashboard() method) that can access the administrative dashboard, but it does not include any code to display the dashboard.

# Impact?
This allows an attacker to discover the administrative REST endpoint URL through string table analysis and use it to execute back-end administrative functionality using curl. This can allow the attacker to access sensitive information and perform unauthorized actions on the backend server.

# How to fix?

In this code, the showAdminDashboard() method is marked as deprecated using the @Deprecated annotation. This will prevent the method from being called and will generate a warning if it is used. This makes it less likely for an attacker to discover and exploit the hidden interface. Additionally, the method could be removed altogether to completely eliminate the vulnerability.

```kotlin
// Import the necessary modules
import android.app.Activity
import android.os.Bundle
import android.widget.Toast

// Define the Activity class
class MainActivity: Activity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        // Show a Toast message
        Toast.makeText(this, "Hello, world!", Toast.LENGTH_SHORT).show()
    }

    // Method to access the administrative dashboard
    @Deprecated("This method is no longer supported.")
    fun showAdminDashboard() {
        // TODO: Implement the administrative dashboard
    }
}
```
