# Heartbleed

The code example below is vulnerable to Heartbleed

```python
import OpenSSL

def get_secret_data(url):
    context = OpenSSL.SSL.Context(OpenSSL.SSL.TLSv1_METHOD)
    conn = OpenSSL.SSL.Connection(context, socket.socket(socket.AF_INET, socket.SOCK_STREAM))
    conn.connect((url, 443))
    conn.send(b'GET /secret_data HTTP/1.1\r\nHost: ' + url.encode() + b'\r\n\r\n')
    response = conn.recv(4096)
    secret_data = response.split(b'\r\n\r\n')[1]
    conn.shutdown()
    conn.close()
    return secret_data
```

# Why it's vulnerable?
It does not properly validate the length of the server's response

# How to fix?

Update your OpenSSL library to the latest version and add the following lines of code to validate the response length:

```python
import OpenSSL

def get_secret_data(url):
    context = OpenSSL.SSL.Context(OpenSSL.SSL.TLSv1_METHOD)
    conn = OpenSSL.SSL.Connection(context, socket.socket(socket.AF_INET, socket.SOCK_STREAM))
    conn.connect((url, 443))
    conn.send(b'GET /secret_data HTTP/1.1\r\nHost: ' + url.encode() + b'\r\n\r\n')
    response = conn.recv(4096)
    
    # Check the length of the response to prevent Heartbleed
    payload = response[4:]
    payload_length = len(payload)
    if payload_length != response_length:
        raise Exception('Invalid payload length')
    
    secret_data = payload.split(b'\r\n\r\n')[1]
    conn.shutdown()
    conn.close()
    return secret_data
```
