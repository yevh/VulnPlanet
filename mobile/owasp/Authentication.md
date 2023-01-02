# Insecure Authentication

The code examples below are vulnureable to Insecure Authentication

Coverage:

- iOS
- Android

# iOS

Failing to verify that service requests are associated with a known user

```
import Foundation
import Network

class ServiceManager {
  let connection: NWConnection
  let service = NWListener.Service(name: "MyService")

  init(host: String, port: Int) {
    self.connection = NWConnection(to: .hostAndPort(host, port), using: .tls)
    self.connection.start(queue: .main)
  }

  func sendRequest(request: String) {
    connection.send(content: request.data(using: .utf8), completion: .contentProcessed({ error in
      if let error = error {
        print("Error sending request: \(error)")
      }
    }))
  }
}
```

# Why it's vulnerable?
In this app, the ServiceManager class is used to send service requests to the app's backend server. The sendRequest() method sends the specified request to the server over a secure TLS connection. However, this app is vulnerable to insecure authentication because it does not verify that the incoming request is associated with a known user.

# Impact?
An attacker could exploit this vulnerability by sending service requests to the app's backend server and anonymously executing functionality that affects legitimate users of the app. For example, an attacker could send a request that triggers a denial of service attack or modifies the app's data in a way that affects the legitimate users.

# How to fix?
To prevent this vulnerability, the app should verify that service requests are associated with a known user before processing them. This ensures that only authenticated users are able to execute functionality on the app's backend server. Here is an updated version of the app that verifies that service requests are associated with a known user:

```
import Foundation
import Network

class ServiceManager {
  let connection: NWConnection
  let service = NWListener.Service(name: "MyService")
  
  // Store a map of user IDs to session keys to keep track of authenticated users.
  var sessions = [String: String]()

  init(host: String, port: Int) {
    self.connection = NWConnection(to: .hostAndPort(host, port), using: .tls)
    self.connection.start(queue: .main)
  }

  func sendRequest(userID: String, sessionKey: String, request: String) {
    // Verify that the user ID and session key are valid and match a known authenticated user.
    if let storedSessionKey = sessions[userID], storedSessionKey == sessionKey {
      connection.send(content: request.data(using: .utf8), completion: .contentProcessed({ error in
        if let error = error {
          print("Error sending request: \(error)")
        }
      }))
    } else {
      print("Invalid user ID or session key")
    }
  }
}
```

In this updated version of the app, the sendRequest() method verifies that the user ID and session key provided by the caller match a known authenticated user. This ensures that only authenticated users are able to send service requests to the app's backend server. If the user ID and session key are invalid or do not match a known authenticated user, the request is not sent to the server. This eliminates the insecure authentication vulnerability and prevents attackers from anonymously executing functionality on the app's backend server.

# Android

Failing to verify that service requests are associated with a known user

```
import android.app.Activity
import java.net.HttpURLConnection

class MainActivity : Activity() {
  fun sendRequest(url: String, request: String) {
    val connection = HttpURLConnection(url)
    connection.requestMethod = "POST"
    connection.doOutput = true
    connection.outputStream.write(request.toByteArray())
    connection.outputStream.close()
  }
}
```

# Why it's vulnerable?
In this app, the sendRequest() method sends a service request to the app's backend server. The request is sent over an HTTP connection, but the app is vulnerable to insecure authentication because it does not verify that the incoming request is associated with a known user.

# Impact?
An attacker could exploit this vulnerability by sending service requests to the app's backend server and anonymously executing functionality that affects legitimate users of the app. For example, an attacker could send a request that triggers a denial of service attack or modifies the app's data in a way that affects the legitimate users.

# How to fix?
To prevent this vulnerability, the app should verify that service requests are associated with a known user before processing them. This ensures that only authenticated users are able to execute functionality on the app's backend server. Here is an updated version of the app that verifies that service requests are associated with a known user:

```
import android.app.Activity
import java.net.HttpURLConnection

class MainActivity : Activity() {
  // Store a map of user IDs to session keys to keep track of authenticated users.
  var sessions = HashMap<String, String>()

  fun sendRequest(userID: String, sessionKey: String, url: String, request: String) {
    // Verify that the user ID and session key are valid and match a known authenticated user.
    if (sessions[userID] == sessionKey) {
      val connection = HttpURLConnection(url)
      connection.requestMethod = "POST"
      connection.doOutput = true
      connection.outputStream.write(request.toByteArray())
      connection.outputStream.close()
    } else {
      println("Invalid user ID or session key")
    }
  }
}
```

In this updated version of the app, the sendRequest() method verifies that the user ID and session key provided by the caller match a known authenticated user. This ensures that only authenticated users are able to send service requests to the app's backend server. If the user ID and session key are invalid or do not match a known authenticated user, the request is not sent to the server. This eliminates the insecure authentication vulnerability and prevents attackers from anonymously executing functionality on the app's backend server.
