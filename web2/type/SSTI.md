# Server Side Template Injection (SSTI)

The code below is vulnureable to SSTI

```
from flask import Flask, render_template

app = Flask(__name__)

@app.route('/')
def index():
    # Get the user-provided message from the query string
    message = request.args.get('message')

    # Use the user-provided message as the title of the page
    return render_template('index.html', title=message)

if __name__ == '__main__':
    app.run()
```

# Why it's vulnerable?
This code sets up a Flask app with a single route that renders a page using the user-provided message parameter as the title of the page. However, if the user supplies a message containing template code, it will be executed on the server when the page is rendered.

# Impact?
For example, if a user sends a request to http://localhost:5000/?message={{config.SECRET_KEY}}, the page will display the value of the SECRET_KEY configuration variable.

# How to fix?
To prevent this type of attack, it's important to properly sanitize user-provided input to ensure it doesn't contain any malicious code. In this case, you could use the escape function provided by the Jinja2 template engine to escape any special characters in the message before rendering it on the page:
