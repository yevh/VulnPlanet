# Insecure Authorization

The code examples below are vulnureable to Insecure Authorization

Coverage:

- iOS
- Android

# iOS

Failing to validate the actor ID associated with a bearer token

```swift
import Foundation

class APIRequest {
  // Send a request to the specified API endpoint.
  func send(endpoint: String, actorID: String, token: String) {
    let url = URL(string: endpoint)
    var request = URLRequest(url: url!)
    request.addValue("Bearer \(token)", forHTTPHeaderField: "Authorization")
    let task = URLSession.shared.dataTask(with: request) { data, response, error in
      // Process the response data.
    }
    task.resume()
  }
}
```

# Why it's vulnerable?
In this app, the ```send()``` method sends a request to the specified API endpoint with the given actor ID and bearer token. However, the app does not validate the actor ID associated with the bearer token.

# Impact?
An attacker can modify the actor ID and access account information of other users.

# How to fix?
To prevent this vulnerability, the app should validate the actor ID associated with the bearer token to ensure that only authorized users can access the requested account information. Here is an updated version of the app that includes this validation:

```kotlin
import Foundation

class APIRequest {
  // Send a request to the specified API endpoint.
  func send(endpoint: String, actorID: String, token: String) {
    let url = URL(string: endpoint)
    var request = URLRequest(url: url!)
    request.addValue("Bearer \(token)", forHTTPHeaderField: "Authorization")

    // Verify that the actor ID associated with the bearer token matches the given actor ID.
    if verifyActorID(actorID, token) {
      let task = URLSession.shared.dataTask(with: request) { data, response, error in
        // Process the response data.
      }
      task.resume()
    } else {
      println("Invalid actor ID")
    }
  }

  // Verify that the actor ID associated with the given bearer token matches the expected actor ID.
  func verifyActorID(actorID: String, token: String) -> Bool {
    // Look up the actor ID associated with the bearer token in the database.
    let storedActorID = getActorID(token)
    return storedActorID == actorID
  }

  // Get the actor ID associated with the given bearer token from the database.
  func getActorID(token: String) -> String {
    // Query the database using the token as the key.
    // Return the actor ID associated with the token.
  }
}
```

In this updated version of the app, the ```send()``` method calls the ```verifyActorID()``` method to validate the actor ID associated with the bearer token before sending the request. The ```verifyActorID()``` method queries the database to look up the actor ID associated with the token and compares it to the expected actor ID. If the actor IDs do not match, the request is not sent and an error message is printed. 

# Android

Failing to validate the actor ID associated with a bearer token

```kotlin
import java.io.BufferedReader
import java.io.InputStreamReader
import java.net.HttpURLConnection
import java.net.URL

class APIRequest {
  // Send a request to the specified API endpoint.
  fun send(endpoint: String, actorID: String, token: String) {
    val url = URL(endpoint)
    val connection = url.openConnection() as HttpURLConnection
    connection.setRequestProperty("Authorization", "Bearer $token")
    connection.connect()

    val input = BufferedReader(InputStreamReader(connection.getInputStream()))
    val response = input.readLine()
    input.close()

    // Process the response data.
  }
}
```

# Why it's vulnerable?
In this app, the ```send()``` method sends a request to the specified API endpoint with the given actor ID and bearer token. However, the app does not validate the actor ID associated with the bearer token.

# Impact?
An acttacker can modify the actor ID and access account information of other users.

# How to fix?
To prevent this vulnerability, the app should validate the actor ID associated with the bearer token to ensure that only authorized users can access the requested account information. Here is an updated version of the app that includes this validation:

```kotlin
import java.io.BufferedReader
import java.io.InputStreamReader
import java.net.HttpURLConnection
import java.net.URL

class APIRequest {
  // Send a request to the specified API endpoint.
  fun send(endpoint: String, actorID: String, token: String) {
    val url = URL(endpoint)
    val connection = url.openConnection() as HttpURLConnection
    connection.setRequestProperty("Authorization", "Bearer $token")

    // Verify that the actor ID associated with the bearer token matches the given actor ID.
    if (verifyActorID(actorID, token)) {
      connection.connect()

      val input = BufferedReader(InputStreamReader(connection.getInputStream()))
      val response = input.readLine()
      input.close()

      // Process the response data.
    } else {
      System.out.println("Invalid actor ID")
    }
  }

  // Verify that the actor ID associated with the given bearer token matches the expected actor ID.
  fun verifyActorID(actorID: String, token: String): Boolean {
    // Look up the actor ID associated with the bearer token in the database.
    val storedActorID = getActorID(token)
    return storedActorID == actorID
  }

  // Get the actor ID associated with the given bearer token from the database.
  fun getActorID(token: String): String {
    // Query the database using the token as the key.
    // Return the actor ID associated with the token.
  }
}
```

In this updated version of the app, the ```send()``` method calls the ```verifyActorID()``` method to validate the actor ID associated with the bearer token before sending the request. The ```verifyActorID()``` method queries the database to look up the actor ID associated with the token and compares it to the expected actor ID. If the actor IDs do not match, the request is not sent and an error message is printed. 
