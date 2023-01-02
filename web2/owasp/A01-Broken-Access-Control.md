# A01 Broken Access Control

Access control enforces policy such that users cannot act outside of their intended permissions. Failures typically lead to unauthorized information disclosure, modification, or destruction of all data or performing a business function outside the user's limits. 

- **Coverage** 
     - JS
     - Java
     - Go
     - Python
     - PHP

# JS

## Vulnureable code example

For example, the following admin endpoint exists and can be accessed by anyone

```
app.use('/admin', function (req, resp) {
    resp.end('Admin functionality...');
});
```

## Prevention code example

Let's add checks if session is established and that the current user is an administrator

```
function checkAdmin(req, resp, next) {
    const username = req.session.user_id;
    if (!username) {
        resp.redirect('/login.html'); //Redirect to Login
    } 
    else {
        const user = db.fetchUser(username);
        if (user.isAdmin) {
            next(); // Next middleware stage or handler
        } 
        else {
            resp.redirect('/login.html'); //Redirect to Login
            return;
        }
    }
}
```

Let's plugged that into every endpoint or router

```
app.use('/admin', checkAdmin, function (req, resp) {
    resp.end('Admin functionality...');
});
```

# Java

## Vulnureable code example

Spring controller does not explicitly enforce any authentication

```
@RestController
@RequestMapping(path = "/admin")
public class AdminApi {
    @PostMapping(path = "action")
    public ResponseEntity doAction() {
        // Some Admin func
    }
}
```

## Prevention code example

Implement an exhaustive authentication mechanism in which some endpoints are allowed, and all others are explicitly forbidden so as to make the application more resilient to changes:

```
http
    .antMatchers(HttpMethod.GET, "/", "/index.html", "/css/**", "/js/**").permitAll()
    .anyRequest().authenticated();
```

# Go

## Vulnureable code example

Administrative functionality that allows a user list to be shown when a specific URL is called. The /admin/users URI is available to everyone who provides a cookie with the name set to a username. A malicious actor could manipulate the HTTP request and add the Cookie header without performing a successful authentication. Since the authentication routine only checks for the presence of the username cookie, access is granted.

```
func Users(w http.ResponseWriter, r * http.Request) {
  cookieUser, _: = r.Cookie("username")
  if cookieUser == nil {
      http.Redirect(w, r, "/login?errMsg=User+Not+Authorized", http.StatusFound)
      return
  }
  // show user list
}
```

## Prevention code example

Implement session management using Gorilla’s Sessions package. Upon successful authentication, the application returns the session cookie go-session, a random base64 encoded string. The string does not contain user information. The user’s session information is stored server-side in a Map object, where the value of the go-session cookie is the key.

```
package main
import (
  "fmt"
  "net/http"
  "github.com/gorilla/sessions"
)
var (
  key = [] byte(GenerateRandomKey(16)) 
  store = sessions.NewCookieStore(key)
)
func users(w http.ResponseWriter, r * http.Request) {
  session, _: = store.Get(r, "go-session")
  // Check if user is authenticated
  if auth, ok: = session.Values["authenticated"].(bool);
  !ok || !auth {
      http.Error(w, "Forbidden", http.StatusForbidden)
      return
    }
    // List Users
    fmt.Fprintln(w, userList)
}
func login(w http.ResponseWriter, r * http.Request) {
  session, _: = store.Get(r, "cookie-name")
  username: = r.Form["username"][0]
  password: = r.Form["password"][0]
  if areUserCredentialsCorrect(username, password) {
    // Set user as authenticated
    session.Values["authenticated"] = true
    session.Values["username"] = username
    session.Save(r, w)
    // redirect user to authenticated page
    return
  }
  http.Error(w, "Forbidden", http.StatusForbidden)
  return
}
func logout(w http.ResponseWriter, r * http.Request) {
  session, _: = store.Get(r, "cookie-name")
  // Revoke users authentication
  session.Values["authenticated"] = false
  session.Save(r, w)
}
func main() {
  http.HandleFunc("/admin/users", users)
  http.HandleFunc("/login", login)
  http.HandleFunc("/logout", logout)
  http.ListenAndServe(":8050", nil)
}
```

# Python

## Vulnureable code example

Flask web application, which has an administrative functionality to delete the database when a specific URL is called.

```
@app.route('/admin/init', methods=['POST'])
def reinitialize():
    cursor.execute("DROP DATABASE analytics")
    return 'Database has been dropped
```

## Prevention code example

Flask-Session is an extension for Flask that adds support for server-side sessions to a web application.

```
from flask import Flask, session, abort
from flask.ext.session import Session

app = Flask(__name__)
app.config.from_object(__name__)
Session(app)

@app.route('/admin/init')
def admin_init():
    if not session.get('user', 'is_authenticated')
      abort(401)
    ...
```

# PHP

## Vulnureable code example

Symfony controller below provides an administrative endpoint that is used to fetch sensitive information about users. The class lacks the security annotation that restricts its access to authenticated users

```
/**
 * @Route("/admin/users")
 */
class UsersController extends Controller
{
    /**
     * @Route("/", name="admin_users_index")
     * @Method("GET")
     */
    public function indexAction(Request $request)
    {
        // get user list
        $manager = $this->getDoctrine()->getManager();
        $users = $manager->getRepository('AppBundle:User')->findAll();

        // render the template
        return $this->render('admin/users/index.html.twig', [
          'users' => $users
        ]);
    }
}
```

## Prevention code example

In Symfony, access to specific controllers can be restricted by annotations. 

```
/**
 * @Route("/admin/users")
 * @Security("has_role('ROLE_ADMIN')")
 */
class UsersController extends Controller {/*...*/};
```
