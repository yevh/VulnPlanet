# A05 Security Misconfiguration

The application might be vulnerable if the application is:

1. Missing appropriate security hardening across any part of the application stack or improperly configured permissions on cloud services.
2. Unnecessary features are enabled or installed (e.g., unnecessary ports, services, pages, accounts, or privileges).
3. Default accounts and their passwords are still enabled and unchanged.
4. Error handling reveals stack traces or other overly informative error messages to users.
5. For upgraded systems, the latest security features are disabled or not configured securely.
6. The security settings in the application servers, application frameworks (e.g., Struts, Spring, ASP.NET), libraries, databases, etc., are not set to secure values.
7. Missing of CSRF protection
8. The server does not send security headers or directives, or they are not set to secure values.

- **Coverage** 
     - JS
     - Java
     - Go
     - Python
     - PHP

# JS

Node.js does not provide a built-in protection against CSRF attacks; the developer must manually implement it by checking the anti-CSRF tokens or using one of the many, well-tested libraries and frameworks. 

## Vulnureable code example

Express.js CSURF middleware protection is not found on an unsafe HTTP method like POST method.

```javascript
let csrf = require('csurf');
let express = require('express');

let csrfProtection = csrf({ cookie: true });

let app = express();

// Sensitive: this operation doesn't look like protected by CSURF middleware (csrfProtection is not used)
app.post('/money_transfer', parseForm, function (req, res) {
  res.send('Money transferred');
});
```

Protection provided by Express.js CSURF middleware is globally disabled on unsafe methods.

```javascript
let csrf = require('csurf');
let express = require('express');

app.use(csrf({ cookie: true, ignoreMethods: ["POST", "GET"] })); // Sensitive as POST is unsafe method

```

## Prevention code example

Express.js CSURF middleware protection is used on unsafe methods.

```javascript
let csrf = require('csurf');
let express = require('express');

let csrfProtection = csrf({ cookie:  true });

let app = express();

app.post('/money_transfer', parseForm, csrfProtection, function (req, res) { // Compliant
  res.send('Money transferred')
});
```

Protection provided by Express.js CSURF middleware is enabled on unsafe methods.

```javascript
let csrf = require('csurf');
let express = require('express');

app.use(csrf({ cookie: true, ignoreMethods: ["GET"] })); // Compliant
```

# Java

## Vulnureable code example

Spring Security provides by default a protection against CSRF attacks which can be disabled.

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.csrf().disable(); // Sensitive: csrf protection is entirely disabled
   // or
    http.csrf().ignoringAntMatchers("/route/"); // Sensitive: csrf protection is disabled for specific routes
  }
}
```

## Prevention code example

Spring Security CSRF protection is enabled by default, do not disable it.

```java
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

  @Override
  protected void configure(HttpSecurity http) throws Exception {
    // http.csrf().disable(); // Compliant
  }
}
```


# Go

## Vulnureable code example

Booking confirmation form with code snippets shown below

```html
{%include "header.html" %}

<h1>Confirm hotel booking</h1>

<form method="POST" action="{%url "Hotels.ConfirmBooking" hotel.HotelId%}">
  <p>
    <strong>Name:</strong> {{hotel.Name}}
  </p>
  <p>
    <strong>Address:</strong> {{hotel.Address}}
...
```

Then the sensitive part is visible as below

```html
...
 <p>
    <strong>Credit card #:</strong> {{booking.CardNumber}}
    <input type="hidden" name="booking.CardNumber" value="{{booking.CardNumber}}">
    <input type="hidden" name="booking.NameOnCard" value="{{booking.NameOnCard}}">
    <input type="hidden" name="booking.CardExpMonth" value="{{booking.CardExpMonth}}">
    <input type="hidden" name="booking.CardExpYear" value="{{booking.CardExpYear}}">
    <input type="hidden" name="booking.Smoking" value="{{booking.Smoking}}">
  </p>

  <p class="buttons">
    <input type="submit" value="Confirm" name="confirm">
    <input type="submit" value="Revise" name="revise">
    <a href="{%url "Hotels.Show" hotel.HotelId%}">Cancel</a>
  </p>
</form>
```

## Prevention code example

```gin-csrf``` package is used for prevention.

```go
...
func main() {
	r := gin.Default()
	store := cookie.NewStore([]byte("secret"))
	r.Use(sessions.Sessions("mysession", store))
	r.Use(csrf.Middleware(csrf.Options{
		Secret: "secret123",
		ErrorFunc: func(c *gin.Context) {
			c.String(400, "CSRF token mismatch")
			c.Abort()
		},
	}))
...
```


# Python

## Vulnureable code example

For a Django application, the code is sensitive when, ```django.middleware.csrf.CsrfViewMiddleware``` is not used in the Django settings.

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
] # Sensitive: django.middleware.csrf.CsrfViewMiddleware is missing
```

The CSRF protection is disabled on a view:

```python
@csrf_exempt
def example(request):
    return HttpResponse("default")
```

## Prevention code example

For a Django application, it is recommended to protect all the views with ```django.middleware.csrf.CsrfViewMiddleware```

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware', # Compliant
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

Not disable the CSRF protection on specific views:

```python
def example(request):
    return HttpResponse("default")
```


# PHP

## Vulnureable code example

For Laravel VerifyCsrfToken middleware

```php
use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    protected $except = [
        'api/*'
    ]; 
}
```

## Prevention code example

For Laravel VerifyCsrfToken middleware

```php
use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    protected $except = [];
}
```
