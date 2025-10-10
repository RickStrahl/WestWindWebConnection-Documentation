Windows Authentication comes in two flavors on IIS servers: via Basic Authentication or NTLM (NT Lan Manager) security. Both integrate with the security of the box that the server is running on, allowing you to validate user accounts against Windows accounts.

Basic Auth can only be prompted from within an application, Windows (NTLM) Auth however can also be configured at the directory level for access to physical files on disk via ACLs. Setting permissions on a file prevents that file from being served if permissions for IUSR_ does not allow it. Instead a login dialog is presented.

Applications can request authentication by sending a HTTP 401 Status code response (use the wwProcess::Authenticate function with cAuthenticationMode set to Basic). This allows the application to request authentication and also check for authenticated users by checking the Request.GetAuthenticatedUserName() function.

Both Basic and Windows Authentication work against Windows User accounts so users must exist as Windows Accounts in order to be used for Authentication.

### Turn on Authentication
First and most importantly make sure that you turn on either Windows or Basic Authentication on your Web Server. On IIS go to the default Web site and select the Security or Authentication icon. Enable either Windows or Basic auth there. You can enable both, but IIS effectively ends up using only Windows (NTLM) authentication when both are enableed.

### Using  Authentication in code
Dealing with authenticating users is a two step process of asking for authentication and then checking for the authentication.

The easiest way to do Authentication in Web Connection is to use the [wwProcess::Authenticate()](vfps://Topic/wwProcess%3A%3AAuthenticate) method, which handles both in a single method call. Login must be called in a  central place or on every request that requires authentication. The most common place to use Login is in the wwProcess::OnProcessInit() method so that every request can be checked for a login:

```foxpro
***Check to see if ANY valid login entered
IF !THIS.AUTHENTICATE("ANY")
   * **access denied - Auth Dialog pops up
   RETURN
ENDIF

... access allowed - code continues
```

You can use this type of code globally as I've done above in the `OnProcessInit()` method, or on individual Process methods. For many applications that require a login throughout the application, the global approach is best.

### Call it early
This code should be called early on in a request either at the top of Process method, or in OnLoad() of Web Control page or - if the login can be globalized in some way - in wwProcess::OnProcessInit().

If I log in with `rstrahl`, the first time this request is accessed `rstrahl` is not logged in so Authenticate() generates a request to authenticate the user through Basic Authentication. The Web Browser pops up an Authentication dialog. The user enters user name and password and they are sent to the server which validates them against the Windows Users set up on the server. If a match is found that username is returned as part of the HTTP request.

![](IMAGES\HOWITWORKS\LOGINDIALOG.GIF)

If the user types in the correct Windows user information the same request that triggered this authentication request is re-run and the user is considered authenticated. Once the right credentials were entered IIS and the browser both will continue to pass the username forward without requiring logging in again.

> #### @icon-warning Logging out
> Note that with Windows/Basic Authentication there's no way to log out other than shutting down the browser. The browser and Web Server share a token that is passed back and forth and unless you shut down this token keeps on getting passed. The credential token is tied to a specific virtual directory.

If you need to retrieve the username logged in on the server for logging or other operational purposes you can retrieve it with  [Request.GetAuthenticatedUser()](vfps://Topic/_S850QHU4X).

### wwProcess::Authenticate - high level logins
Authenticate() basically manages the entire login process by checking for a login and if found letting code go on, and if not forcing the Authentication request back to the server. By the time Authenticate succeeds you're guaranteed that the request passed Authentication. You can use the username from GetAuthenticatedUser() if you need to prefill username information in an app or you need to log the user's name.

The Authenticate() method takes a username or user identity to validate against:
* **ANY** - any validated user
* **WCINI** - the value defined in the Web Connection Server Configuration file (web.config, WebConnectionServerConfiguration.xml, wc.ini) and the `AdminAccount` Key
* **User1** - a single username
* **User1,User2,User3** - a comma delimited list of usernames

Once authenticated the user's credentials are passed forward from the client on every Web request until the browser is shut down or you force another login. This means you'll see the login dialog once, and subsequent hits simply read the valid username and continue on.

### Note: Basic Authentication uses Clear Text Passwords
Keep in mind that Basic Authentication sends a password over the wire! Therefore, login dialogs such as this are inherently insecure. If you're worried about security beyond password information, it's recommended that you also use an SSL/HTTPS request to force the password authentication. That way the request information will be encrypted on the way to the server.

Windows (NTLM) Authentication does not have this problem so it's almost always preferrable to use Windows Authentication rather than basic authentication.