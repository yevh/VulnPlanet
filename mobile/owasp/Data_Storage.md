# Insecure Data Storage

The code examples below are vulnureable to Insecure Data Storage

Coverage:

- iOS
- Android

# iOS

Storing user credentials in a plain text SQLite database

```swift
import UIKit
import SQLite

class ViewController: UIViewController {
  let db = try! Connection("credentials.sqlite")
  let credentials = Table("credentials")
  let username = Expression<String>("username")
  let password = Expression<String>("password")

  func login(username: String, password: String) {
    // Store the username and password in the database.
    try! db.run(credentials.insert(self.username <- username, self.password <- password))
  }
}
```

# Why it's vulnerable?
In this app, the ```login()``` method is called when the user enters their credentials and logs in to the app. The username and password are then stored in a SQLite database using the SQLite library. However, this app is vulnerable to insecure data storage because it stores the user's credentials in a plain text database.

# Impact?
Storing sensitive information, such as passwords, in plain text is insecure because it can be accessed by anyone who has access to the database file. In this case, an attacker who gains access to the device could easily extract the user's credentials from the database.

# How to fix?
To prevent this vulnerability, the app should encrypt the user's credentials before storing them in the database. This ensures that the credentials are protected and cannot be accessed by anyone without the proper encryption key. Here is an updated version of the app that encrypts the user's credentials before storing them in the database:

```swift
import UIKit
import SQLite
import CryptoSwift

class ViewController: UIViewController {
  let db = try! Connection("credentials.sqlite")
  let credentials = Table("credentials")
  let username = Expression<String>("username")
  let password = Expression<String>("password")

  func login(username: String, password: String) {
    // Encrypt the password using AES encryption.
    let encryptedPassword = try! password.encrypt(cipher: AES(key: "my_encryption_key", iv: "my_iv_vector"))

    // Store the username and encrypted password in the database.
    try! db.run(credentials.insert(self.username <- username, self.password <- encryptedPassword))
  }
}
```

In this updated version of the app, the ```login()``` method encrypts the user's password using the AES algorithm and a secret encryption key before storing it in the database. This ensures that the password is encrypted and cannot be accessed by anyone without the proper encryption key. 

# Android

Storing user credentials in a plain text SQLite database

```kotlin
import android.app.Activity
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper

class MainActivity : Activity() {
  private val dbHelper = SQLiteOpenHelper(this, "credentials.sqlite", null, 1)
  private val db = dbHelper.writableDatabase

  fun login(username: String, password: String) {
    // Store the username and password in the database.
    db.execSQL("INSERT INTO credentials (username, password) VALUES (?, ?)", arrayOf(username, password))
  }
}
```

# Why it's vulnerable?
In this app, the ```login()``` method is called when the user enters their credentials and logs in to the app. The username and password are then stored in a SQLite database using the SQLiteOpenHelper class. However, this app is vulnerable to insecure data storage because it stores the user's credentials in a plain text database.

# Impact?
Storing sensitive information, such as passwords, in plain text is insecure because it can be accessed by anyone who has access to the database file. In this case, an attacker who gains access to the device could easily extract the user's credentials from the database.

# How to fix?
To prevent this vulnerability, the app should encrypt the user's credentials before storing them in the database. This ensures that the credentials are protected and cannot be accessed by anyone without the proper encryption key. Here is an updated version of the app that encrypts the user's credentials before storing them in the database:

```kotlin
import android.app.Activity
import android.database.sqlite.SQLiteDatabase
import android.database.sqlite.SQLiteOpenHelper
import javax.crypto.Cipher
import javax.crypto.KeyGenerator
import javax.crypto.SecretKey

class MainActivity : Activity() {
  private val dbHelper = SQLiteOpenHelper(this, "credentials.sqlite", null, 1)
  private val db = dbHelper.writableDatabase
  private val keyGenerator = KeyGenerator.getInstance("AES")
  private val key = keyGenerator.generateKey()
  private val cipher = Cipher.getInstance("AES/CBC/PKCS7Padding")

  fun login(username: String, password: String) {
    // Encrypt the password using AES encryption.
    cipher.init(Cipher.ENCRYPT_MODE, key)
    val encryptedPassword = cipher.doFinal(password.toByteArray(Charsets.UTF_8))

    // Store the username and encrypted password in the database.
    db.execSQL("INSERT INTO credentials (username, password) VALUES (?, ?)", arrayOf(username, encryptedPassword))
  }
}
```

In this updated version of the app, the login() method encrypts the user's password using the AES algorithm and a secret encryption key before storing it in the database. This ensures that the password is encrypted and cannot be accessed by anyone without the proper encryption key.
