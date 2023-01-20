# Server-side request forgery (SSRF) 

The code below is vulnureable to Server-side request forgery (SSRF)


```go
package main

import (
    "net/http"
    "io/ioutil"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        // Get the user-provided URL from the query string
        url := r.URL.Query().Get("url")

        // Make a request to the user-provided URL
        resp, err := http.Get(url)
        if err != nil {
            // Handle the error
            return
        }
        defer resp.Body.Close()

        // Do something with the response
        body, err := ioutil.ReadAll(resp.Body)
        if err != nil {
            // Handle the error
            return
        }
        w.Write(body)
    })

    http.ListenAndServe(":8080", nil)
}
```

# Why it's vulnerable?
This code sets up a simple HTTP server that uses the net/http package to make a request to a URL provided by the user in the query string. However, if the user supplies a malicious URL, they can use the server to make unauthorized requests to internal network resources.

# Impact?
For example, if the user sends a request to ```http://localhost:8080/?url=http://localhost:8080/secret```, the server will make a request to the /secret endpoint on localhost:8080, potentially allowing the attacker to access sensitive information or perform unauthorized actions on the internal network.

# How to fix?
To prevent this type of attack, it's important to properly validate and sanitize user-provided input before using it to make requests. In this case, you could use the ```url.Parse()``` function to parse the URL and check if it is a valid URL before making the request:

```go
package main

import (
    "net/http"
    "io/ioutil"
    "net/url"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        // Get the user-provided URL from the query string
        urlStr := r.URL.Query().Get("url")

        // Parse the URL to check if it is valid
        u, err := url.Parse(urlStr)
        if err != nil {
            // The URL is invalid, return an error
            http.Error(w, "Invalid URL", http.StatusBadRequest)
            return
        }

        // Make a request to the user-provided URL
        resp, err := http.Get(u.String())
        if err != nil {
            // Handle the error
            return
        }
        defer resp.Body.Close()

        // Do something with the response
        body, err := ioutil.ReadAll(resp.Body)
        if err != nil {
            // Handle the error
            return
        }
        w.Write(body)
    })

    http.ListenAndServe(":8080", nil)
}
```

This code uses the ```url.Parse()``` function to parse the URL and check if it is a valid URL before making the request. If the URL is not a valid URL, the request will be rejected with a bad
