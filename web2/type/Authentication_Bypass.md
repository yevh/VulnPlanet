# Authentication Bypass

The code below is vulnureable to Authentication Bypass

```
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
      (...)
            httpRequest = (HttpServletRequest)request;
            logger.debug("doFilter url: " + httpRequest.getRequestURL().toString());
            boolean isAuthenticated = this.authenticateUser(httpRequest);
              ^^^ 1.5) invokes authenticateUser() (function shown below)
              
            String samlLogoutRequest;
            if(!isAuthenticated) {
              ^^^ 1.6) if authenticateUser() returns false, we go into this branch
              
                samlLogoutRequest = request.getParameter("SAMLResponse");
                logger.info("samlResponse-->" + samlLogoutRequest);
                if(samlLogoutRequest != null) {
                    this.handleSAMLReponse(request, response, chain, samlLogoutRequest);
                } else {
                  ^^^ 1.7) if there is no SAMLResponse HTTP parameter, we go into this branch
                  
                    HttpSession session;
                    ProductAccess userBean;
                    String requestedUri;
                    if(this.isStarshipRequest(httpRequest)) {
                      ^^^ 1.8) checks if isStarshipRequest() returns true (function shown below)
                      
                        session = null != httpRequest.getSession(false)?httpRequest.getSession(false):httpRequest.getSession(true);
                        userBean = (ProductAccess)session.getAttribute("USER_IN_SESSION");
                        if(userBean == null) {
                          ^^^ 1.9) if there is no session server side for this request, follow into this branch...
                          
                            try {
                                userBean = new ProductAccess();
                                userBean.setCredentialId("");
                                userBean.setAdminPasswordReset(true);
                                userBean.setProductId("cloupia_service_portal");
                                userBean.setProfileId(0);
                                userBean.setRestKey(httpRequest.getHeader("X-Starship-Request-Key"));
                                userBean.setStarshipUserId(httpRequest.getHeader("X-Starship-UserName-Key"));
                                userBean.setLoginName("admin");
                                  ^^^ 1.10) and create a new session with the user as "admin"!
                                  
                                userBean.setStarshipSessionId(httpRequest.getHeader("X-Starship-UserSession-Key"));
                                requestedUri = httpRequest.getHeader("X-Starship-UserRoles-Key");
                                userBean.setAccessLevel(requestedUri);
                                if(requestedUri != null && requestedUri.equalsIgnoreCase("admin")) {
                                    AuthenticationManager authmgr = AuthenticationManager.getInstance();
                                    userBean.setAccessLevel("Admin");
                                    authmgr.evaluateAllowedOperations(userBean);
                                }

                                session.setAttribute("USER_IN_SESSION", userBean);
                                session.setAttribute("DEFAULT_URL", STARSHIP_DEFAULT_URL);
                                logger.info("userBean:" + userBean.getAccessLevel());
                            } catch (Exception var12) {
                                logger.info("username/password wrong for rest api access - " + var12.getMessage());
                            }

                            logger.info("userBean: " + userBean.getAccessLevel());
                        }

                        chain.doFilter(request, response);
```

# Why it's vulnerable?

1. The authenticateUser() function is called to check whether the user is authenticated. If this function returns false, the code then checks for the presence of a SAMLResponse HTTP parameter. If no such parameter is present, the code checks whether the request is a starship request by calling the isStarshipRequest() function. If this function returns true, the code creates a new user session with the user set to "admin", and sets the user's access level to "Admin". This means that any user who is able to make a starship request will be granted admin privileges, regardless of whether they are actually authorized to do so. This is a serious security vulnerability, as it could allow unauthorized users to gain access to sensitive information or perform actions that they should not be able to.
2. The code does not properly validate the user's credentials when creating a new session. The code simply sets the user to "admin" and the access level to "Admin", without checking if the user actually has these credentials. This could allow an attacker to create a session with arbitrary user credentials and gain access to sensitive information or perform actions that they should not be able to.
3. The code does not properly handle exceptions when creating a new user session. If an exception is thrown, the code simply logs the error message and continues, without taking any action to prevent the user from gaining unauthorized access. This could allow an attacker to bypass the authentication and authorization checks and gain access to sensitive information or perform actions that they should not be able to.

# Impact?

Overall, this code contains several serious vulnerabilities that could allow unauthorized users to gain access to sensitive information or perform actions that they should not be able to. 

# How to fix?

To address these vulnerabilities, the code should be rewritten to properly validate user credentials, handle exceptions in a secure manner, and prevent unauthorized users from gaining access to sensitive information or performing actions that they are not authorized to do.
