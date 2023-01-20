# Insufficient Cryptography

The code examples below are vulnureable to Insufficient Cryptography

Coverage:

- iOS
- Android

# iOS

Insecure cryptographic algorithm to encrypt sensitive data

```swift
import Foundation
import CommonCrypto

class Encryptor {
  // Use the insecure DES algorithm to encrypt the given data.
  func encrypt(data: Data, key: Data) -> Data? {
    var encryptedData = Data(count: data.count + kCCBlockSizeDES)
    let status = encryptedData.withUnsafeMutableBytes { encryptedBytes in
      data.withUnsafeBytes { dataBytes in
        key.withUnsafeBytes { keyBytes in
          CCCrypt(
            CCOperation(kCCEncrypt),
            CCAlgorithm(kCCAlgorithmDES),
            CCOptions(kCCOptionPKCS7Padding),
            keyBytes,
            kCCKeySizeDES,
            nil,
            dataBytes,
            data.count,
            encryptedBytes,
            encryptedData.count,
            nil
          )
        }
      }
    }

    if status == kCCSuccess {
      encryptedData.count = Int(encryptedData.count) - kCCBlockSizeDES
      return encryptedData
    } else {
      return nil
    }
  }
}
```

# Why it's vulnerable?
In this app, the ```encrypt()``` method uses the insecure DES algorithm to encrypt the given data using the specified key. This app is vulnerable to insufficient cryptography because the DES algorithm is no longer considered secure due to its small key size and other weaknesses.

# Impact?
An attacker could exploit this vulnerability by using a known plaintext attack to recover the encryption key and decrypt the sensitive data. This could expose the app's users to privacy and security risks if the encrypted data contains sensitive information such as passwords or financial data.

# How to fix?
To prevent this vulnerability, the app should use a modern and secure cryptographic algorithm to encrypt the sensitive data. Here is an updated version of the app that uses the AES algorithm to encrypt the data:

```swift
import Foundation
import CommonCrypto

class Encryptor {
  // Use the AES algorithm to encrypt the given data.
  func encrypt(data: Data, key: Data) -> Data? {
    var encryptedData = Data(count: data.count + kCCBlockSizeAES128)
    let status = encryptedData.withUnsafeMutableBytes { encryptedBytes in
      data.withUnsafeBytes { dataBytes in
        key.withUnsafeBytes { keyBytes in
          CCCrypt(
            CCOperation(kCCEncrypt),
            CCAlgorithm(kCCAlgorithmAES),
            CCOptions(kCCOptionPKCS7Padding),
            keyBytes,
            kCCKeySizeAES256,
            nil,
            dataBytes,
            data.count,
            encryptedBytes,
            encryptedData.count,
            nil
          )
        }
      }
    }

    if status == kCCSuccess {
      encryptedData.count = Int(encryptedData.count) - kCCBlockSizeAES128
      return encryptedData
    } else {
      return nil
    }
  }
}
```

In this updated version of the app, the ```encrypt()``` method uses the AES algorithm to encrypt the data.

# Android

Insecure cryptographic algorithm to encrypt sensitive data

```kotlin
import android.util.Base64
import javax.crypto.Cipher
import javax.crypto.spec.SecretKeySpec

class Encryptor {
  // Use the insecure DES algorithm to encrypt the given data.
  fun encrypt(data: ByteArray, key: ByteArray): ByteArray? {
    val cipher = Cipher.getInstance("DES")
    val keySpec = SecretKeySpec(key, "DES")
    cipher.init(Cipher.ENCRYPT_MODE, keySpec)
    return cipher.doFinal(data)
  }
}
```

# Why it's vulnerable?
In this app, the ```encrypt()``` method uses the insecure DES algorithm to encrypt the given data using the specified key. This app is vulnerable to insufficient cryptography because the DES algorithm is no longer considered secure due to its small key size and other weaknesses.

# Impact?
An attacker could exploit this vulnerability by using a known plaintext attack to recover the encryption key and decrypt the sensitive data. This could expose the app's users to privacy and security risks if the encrypted data contains sensitive information such as passwords or financial data.

# How to fix?
To prevent this vulnerability, the app should use a modern and secure cryptographic algorithm to encrypt the sensitive data. Here is an updated version of the app that uses the AES algorithm to encrypt the data:

```kotlin
import android.util.Base64
import javax.crypto.Cipher
import javax.crypto.spec.SecretKeySpec

class Encryptor {
  // Use the AES algorithm to encrypt the given data.
  fun encrypt(data: ByteArray, key: ByteArray): ByteArray? {
    val cipher = Cipher.getInstance("AES")
    val keySpec = SecretKeySpec(key, "AES")
    cipher.init(Cipher.ENCRYPT_MODE, keySpec)
    return cipher.doFinal(data)
  }
}
```

In this updated version of the app, the ```encrypt()``` method uses the AES algorithm to encrypt the data.
