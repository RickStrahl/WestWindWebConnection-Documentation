This method is a Bearer Token based implementation of the [wwSession](VFPS://Topic/_S8413ZE4Y) object that allows you track user state between REST client calls. Most commonly this can be used to map a Bearer Token to a user or customer record.

This is a low level method that only handles Bearer Token to Session mapping.

This class provides for:

* Bearer Token checking for testing for a previously authorized token
* Checks the `Authorization: Bearer <token>` header for a token
* Creates a Session Token in the `wwSession` data store
* Exposes the `wwProcess::oSession`
* Exposes access to the token via `oSession.cSessionId`
* Access to the `oSession.oData.UserId` if mapped
* Can use `SetSessionVar()`/`GetSessionVar()` to read and write persisted data
* Session state can be used to map a user/customer record
* Session state can be used to cache common user values
* Can be used in combination with `Authenticate()` to map `wwUserSecurity` record

Unlike the base `wwSession` class, this class is more lightweight and doesn't use cookies. 

Instead it only reads authorization bearer tokens from the `Authorization` header and simply writes the token into the Session table. No Coookies are used by this class and by default the session isn't auto-saved at the end of each request to keep it light weight.

### Usage
To use this mechanism you'll need two things:

* Implement the Token verification via `InitTokenSession()` in `OnProcessInit()`
* Generate a new Token via some sort of User Validation and creating a new Session


### Checking for and Validating a Bearer Token
The token validation works by looking at all requests and checking for a `Authorization: Bearer ` token in the header and if found trying to map it to an existing `wwSession` record in `THIS.oSession`.
  
The code to initialize and check incoming requests looks like this:

```foxpro
FUNCTION OnProcessInit

Response.Encoding = "UTF8"
Request.lUtf8Encoding = .T.

...

*** Pick up existing token or create a new token
*** and set on the oSession object
THIS.InitTokenSession()

*** Define anonymous requests that don't need validation
lcScriptName = LOWER(JUSTFNAME(Request.GetPhysicalPath()))
llIgnoreLoginRequest = INLIST(lcScriptName,"testpage","signin","signout")

*** Fail if not authorized (ie. a new session has no or failed bearer token)
IF !llIgnoreLoginRequest AND this.oSession.lIsNewSession
   THIS.ErrorResponse("Access Denied. Please sign in first.","401 Unauthorized")
   RETURN .F.
ENDIF

RETURN .T.
```

### Signing In to create a Token and creating a new Session
In order to create a new token you need to create a new token - typically done after you've validated a user login or some other credentials. For simplicity's sake here is a Signin method:

```foxpro
*** A simple REST Process Method  POST Signin.tp  - { username: "email@test.com", password: "superSeekrit" }
FUNCTION Signin
LPARAMETER loCredentials

*** Use whatever custom Authorization you need to assign a token
IF !loBus.AuthorizeUser(loCredentials.UserName, loCredentials.Password)
   RETURN THIS.ErrorResponse(loBus.cErrorMsg,"401 Unauthorized")
ENDIF

*** Create a new Session and optionally assign a mapping user id
*** that links back to a user/customer record in the Application
lcToken = THIS.oSession.NewSession(loBus.oData.UserId)

*** Return the token and expiration (or whatever you choose)
loToken = CREATEOBJECT("EMPTY")
ADDPROPERTY(loToken,"token", lcToken)
ADDPROPERTY(loToken,"expires", DATETIME() + 3600 * 24)

RETURN loToken
ENDFUNC
```

The Authorization process can be anything that returns a true or false operation. It can be as simple as checking to see if username and password are not empty to something as complex as validating user name and password and checking subscription information etc. It's up to you.

### Integration with UserSecurity
If you want more structure for the credential Authorization you can do this by combining this mechanism with UserSecurity Authentication built into Web Connection which can map the Bearer token to a UserSecurity record on your Process class.

You can do this by combining with the `Authenticate()` method, which automatically maps with a `UserSecurity` user record or your own subclassed customized version thereof.


For more information see the topic on [wwRestProcess::Authenticate()](VFPS://Topic/_6R619BPU9)