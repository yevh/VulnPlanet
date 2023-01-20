# Improper Platform Usage

The code examples below are vulnureable to Improper Platform Usage

Coverage:

- iOS
- Android

# iOS

Uses app local storage instead of the keychain to store sensitive data

```swift
import UIKit

class ViewController: UIViewController {
  let userDefaults = UserDefaults.standard

  @IBAction func saveButtonTapped(_ sender: Any) {
    let password = "my_password"
    userDefaults.set(password, forKey: "password")
  }
}
```

# Why it's vulnerable?
In this app, the ```saveButtonTapped()``` method is called when the user taps a button to save their password. The password is then stored in the app's local storage using the UserDefaults class. However, this app is vulnerable to an M1: Improper Platform Usage vulnerability because it uses app local storage to store sensitive data, such as passwords.

# Impact?
App local storage is not secure and can be accessed by other apps on the device or by attackers who have gained access to the device. This means that the user's password could be compromised if the device is lost or stolen, or if the app is hacked.

# How to fix?
To prevent this vulnerability, the app should use the keychain to store sensitive data, such as passwords. The keychain is a secure storage location provided by the iOS platform, which is encrypted and protected by the device's hardware security features. Here is an updated version of the app that uses the keychain to store the user's password:

```swift
import UIKit
import KeychainAccess

class ViewController: UIViewController {
  let keychain = Keychain(service: "com.myapp.password")

  @IBAction func saveButtonTapped(_ sender: Any) {
    let password = "my_password"
    try keychain.set(password, key: "password")
  }
}
```

In this updated version of the app, the saveButtonTapped() method uses the Keychain class from the KeychainAccess library to store the user's password in the keychain. This ensures that the password is encrypted and protected by the device's hardware security features, preventing it from being accessed by other apps or attackers. This eliminates the M1: Improper Platform Usage vulnerability and ensures that the user's password is stored securely.


# Android

Stores sensitive information in shared preferences

```kotlin
import android.content.SharedPreferences

class MainActivity : AppCompatActivity() {
  private val sharedPreferences: SharedPreferences by lazy {
    getSharedPreferences("my_app_prefs", Context.MODE_PRIVATE)
  }

  fun savePassword(password: String) {
    val editor = sharedPreferences.edit()
    editor.putString("password", password)
    editor.apply()
  }
}
```

# Why it's vulnerable?
The ```savePassword()``` method is called to save the user's password to shared preferences. However, this app is vulnerable to an M1: Improper Platform Usage vulnerability because it uses shared preferences to store sensitive information, such as passwords.

# Impact?
Shared preferences are not secure and can be accessed by other apps on the device or by attackers who have gained access to the device. This means that the user's password could be compromised if the device is lost or stolen, or if the app is hacked.

# How to fix?
To prevent this vulnerability, the app should use a secure storage location provided by the Android platform, such as the keystore, to store sensitive information. The keystore is encrypted and protected by the device's hardware security features, making it more secure than shared preferences. Here is an updated version of the app that uses the keystore to store the user's password:

```kotlin
import android.security.keystore.KeyGenParameterSpec
import android.security.keystore.KeyProperties
import java.security.KeyStore
import javax.crypto.Cipher
import javax.crypto.KeyGenerator
import javax.crypto.SecretKey

class MainActivity : AppCompatActivity() {
  private val keyStore = KeyStore.getInstance("AndroidKeyStore")
  private val keyGenerator = KeyGenerator.getInstance(KeyProperties.KEY_ALGORITHM_AES, "AndroidKeyStore")

  fun savePassword(password: String) {
    keyStore.load(null)
    val key = createKey("my_app_key")
    val cipher = Cipher.getInstance(KeyProperties.KEY_ALGORITHM_AES + "/" + KeyProperties.BLOCK_MODE_CBC + "/" + KeyProperties.ENCRYPTION_PADDING_PKCS7)
    cipher.init(Cipher.ENCRYPT_MODE, key)
    val encryptedPassword = cipher.doFinal(password.toByteArray(Charsets.UTF_8))
    // Store the encrypted password in the keystore.
    keyStore.setEntry("my_app_key", KeyStore.SecretKeyEntry(key), KeyProtection.Builder(KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT).build())
  }

  private fun createKey(keyName: String): SecretKey {
    keyGenerator.init(
      KeyGenParameterSpec.Builder(keyName, KeyProperties.PURPOSE_ENCRYPT or KeyProperties.PURPOSE_DECRYPT)
        .setBlockModes(KeyProperties.BLOCK_MODE_CBC)
        .setEncryptionPaddings(KeyProperties.ENCRYPTION_PADDING_PKCS7)
        .build()
    )
    return keyGenerator.generateKey()
  }
}
```

In this updated version of the app, the ```savePassword()``` method encrypts the user's password using the AES algorithm and the device's hardware-backed keystore. The encrypted password is then stored in the keystore using the ```setEntry()``` method. This ensures that the password is encrypted and protected by the device.
