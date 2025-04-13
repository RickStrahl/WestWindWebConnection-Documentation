The Authenticate method uses `UserSecurity` authorization to handle:

* User Authentication by username and password
* Validating an `Authorization` Bearer Token for a valid login
* Logging by removing/invalidating a token and the active session

> `wwRestProcess::Authenticate()` uses the `wwSession` class and requires a call to `InitTokenSession()` (implicitly made if you didn't call it yourself), but it doesn't use Cookies like the base `wwProcess` class does. The REST authentication replaces Cookie authentication with Bearer Token Authentication.

To do this this method can be called in three ways:

* **Authenticate(lcUsername, lcPassword)**  
*User Authentication Mode*  
With two parameters it authenticates a user based on a passed in username and password and updates the new session and various wwProcess authentication properties. As with the no-parameter version all the user related properties are populated if the authentication succeeds. If it fails a `401 Unauthorized` response is returned.

* **Authenticate()**  
*Token Validation Mode*  
With no parameters this method is in Validation Mode validates a user and assigns the various wwProcess authentication properties if the user is authenticated. In essence the User token is mapped to a UserSecurity record (by default - you can override this behavior) if the check succeeds, and if it fails results in a `401 Unauthorized` response.

* **Authenticate("LOGOUT")**  
*Logout Mode*  
This simple operation is meant to remove an existing token. You need to be signed in and provide a Bearer token in the `Authorization` header and that active session and token will then be removed.


This mechanism is based on Bearer Token authentication which assumes that a token is passed in the `Authorization` header of the request coming from the client:

```http
Authorization: Bearer x312d2dasdas2
```

The token is stored using the wwSession object along with user security information.

### Implementation
There are three distinct operations that need to be performed:

* Check if a user is already signed in (in `OnProcessInit()`)
* Sign in a user
* Sign out a user

### Check if a User is Signed in
To check if a user is signed in you need to check on every request that has to be authenticated. The following assumes all requests need to be authenticated except those on the ignore list:

```foxpro
FUNCTION OnProcessInit

... 

*** IMPORTANT: InitTokenSession is required to pick up the Bearer token
***            and load or create a new session
THIS.InitTokenSession()

*** Check for pages that should bypass auth - signin always (not signout though!)
lcScriptName = LOWER(JUSTFNAME(Request.GetPhysicalPath()))
llIgnoreLoginRequest = INLIST(lcScriptName,"testage","signin")

IF !llIgnoreLoginRequest
   *** Check for Authentication here based on the token (note no parameters)
   IF !this.Authenticate()   
	   THIS.ErrorResponse("Access Denied. Please sign in first.","401 Unauthorized")
	   RETURN .F. && Response is handled
   ENDIF
ENDIF

RETURN .T.
ENDFUNC
```

The call to `InitTokenSession()` creates a session based on the passed `Bearer token` in the `Authorization` header.

The call to `Authorize()` maps the token from the session to a `UserSecurity` object and the various Auth properties on the `wwRestProcess` class.

### Signin
In order for users to sign in and create the initial token you need to pass credentials into a specific request - here called `Signin`. You use the credentials to validate the user. By default a username and password is validated against the user security class configured.

```foxpro
FUNCTION SignIn(loCredentials)
LOCAL loToken, llError, lcErrorMsg, lcToken, ltExpires

lcErrorMsg = ""
lcToken = ""
TRY
	*** Sign in: If successful sets oUserSecurity, lIsAuthenticated, cAuthenticatedUser etc.
	IF this.Authenticate(loCredentials.Username, loCredentials.Password)
		lcToken = Session.cSessionId 
		ltExpires = Session.oData.FirstOn + Session.nSessionTimeout
		
		*** Set any custom values you might need
		Session.SetSessionVar("tenant","TENANT_ID")
		
		Session.Save() && Explicit save: Session on REST Services don't save by default		
	ELSE
		lcErrorMsg = "Invalid Credentials..."	    
		llError = .T.
	ENDIF
CATCH TO loException
    lcErrorMsg = loException.Message
	llError = .T.
ENDTRY


IF llError
   RETURN THIS.ErrorResponse(lcErrorMsg,"401 Not Authorized")
ENDIF

*** Response
loToken = CREATEOBJECT("EMPTY")
ADDPROPERTY(loToken,"token", lcToken)
ADDPROPERTY(loToken,"expires", ltExpires)

** Add other client cachable user data here as properties if you need

RETURN loToken
ENDFUNC
*  Signin
```

Once the user has logged in the user is reloaded on every request if a valid token is provided.

### Signout
If you need to explicitly sign out a user you can do so using an explicit method like this:

```foxpro
FUNCTION Signout()
THIS.Authenticate("LOGOUT")
JsonService.IsRawResponse = .T.
Response.Write( [{ message: "Successfully logged out." }])   
ENDFUNC
*   Signout
```

> In order to sign out you have to be signed in with a token. The token determines which session record is removed once you sign out.


### Overriding functionality
As in the HTML `wwProcess` class you can override parts of the Authentication process. For example if you want to use some other authentication mechanism than User Security you can do that by overriding `OnAuthenticateUser` for example, without having to rewrite the entire Authentication logic.