# Insecure Communication

The code examples below are vulnureable to Insecure Communication

Coverage:

- iOS
- Android

# iOS

Failing to inspect the server's certificate during a TLS handshake

```swift
import Foundation
import Network

class ConnectionManager {
  let host: NWEndpoint.Host
  let port: NWEndpoint.Port

  init(host: String, port: Int) {
    self.host = NWEndpoint.Host(host)
    self.port = NWEndpoint.Port(port)
  }

  func connect() {
    let connection = NWConnection(to: .hostAndPort(host, port), using: .tls)
    connection.start(queue: .main)
  }
}
```

# Why it's vulnerable?
In this app, the ConnectionManager class is used to establish a secure connection to a server using TLS. The ```connect()``` method creates a NWConnection object and uses the .tls parameter to specify that the connection should be encrypted using TLS. However, this app is vulnerable to insecure communication because it does not inspect the server's certificate during the TLS handshake.

# Impact?
By default, the NWConnection class will unconditionally accept any certificate offered by the server during the TLS handshake. This means that an attacker who is able to intercept the connection and offer a forged certificate can impersonate the server and establish a man-in-the-middle attack. This destroys the mutual authentication capability of TLS and leaves the app vulnerable to attacks.

# How to fix?
To prevent this vulnerability, the app should inspect the server's certificate during the TLS handshake and verify that it is valid and trusted. This ensures that the app only establishes a connection with the authentic server and not with an attacker who is using a forged certificate. Here is an updated version of the app that verifies the server's certificate during the TLS handshake:

```swift
import Foundation
import Network

class ConnectionManager {
  let host: NWEndpoint.Host
  let port: NWEndpoint.Port

  init(host: String, port: Int) {
    self.host = NWEndpoint.Host(host)
    self.port = NWEndpoint.Port(port)
  }

  func connect() {
    let connection = NWConnection(to: .hostAndPort(host, port), using: .tls)
    connection.tlsOptions = [
      .pkiModule: .default,
      .peerName: host,
      .minimumTLSVersion: .v1_2,
      .maximumTLSVersion: .v1_3
    ]
    connection.start(queue: .main)
  }
}
```

In this updated version of the app, the connect() method sets the tlsOptions property of the NWConnection object to specify the options for the TLS handshake. The .pkiModule option is set to .default to use the system's default certificate trust store


# Android

Failing to inspect the server's certificate during a TLS handshake

```kotlin
import android.app.Activity
import javax.net.ssl.HttpsURLConnection

class MainActivity : Activity() {
  fun connect(url: String) {
    val connection = HttpsURLConnection(url)
    connection.connect()
  }
}
```

# Why it's vulnerable?
In this app, the connect() method creates a HttpsURLConnection object and uses it to establish a connection to the specified server URL. This connection is encrypted using TLS, but the app is vulnerable to insecure communication because it does not inspect the server's certificate during the TLS handshake.

# Impact?
By default, the HttpsURLConnection class will unconditionally accept any certificate offered by the server during the TLS handshake. This means that an attacker who is able to intercept the connection and offer a forged certificate can impersonate the server and establish a man-in-the-middle attack. This destroys the mutual authentication capability of TLS and leaves the app vulnerable to attacks.

# How to fix?
To prevent this vulnerability, the app should inspect the server's certificate during the TLS handshake and verify that it is valid and trusted. This ensures that the app only establishes a connection with the authentic server and not with an attacker who is using a forged certificate. Here is an updated version of the app that verifies the server's certificate during the TLS handshake:

```kotlin
import android.app.Activity
import javax.net.ssl.HttpsURLConnection
import javax.net.ssl.SSLContext
import javax.net.ssl.TrustManagerFactory

class MainActivity : Activity() {
  fun connect(url: String) {
    // Use the system's default trust store to verify the server's certificate.
    val trustManagerFactory = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm())
    trustManagerFactory.init(null as KeyStore?)
    val trustManagers = trustManagerFactory.trustManagers
    val sslContext = SSLContext.getInstance("TLS")
    sslContext.init(null, trustManagers, null)

    // Create a HttpsURLConnection and set the SSLContext to use for the TLS handshake.
    val connection = HttpsURLConnection(url)
    connection.sslSocketFactory = sslContext.socketFactory
    connection.connect()
  }
}
```

In this updated version of the app, the ```connect()``` method uses the system's default trust store to verify the server's certificate during the TLS handshake. This ensures that the app only establishes a connection with the authentic server and not with an attacker who is using a forged certificate. This eliminates the insecure communication vulnerability and ensures that the app is protected against man-in-the-middle attacks.
