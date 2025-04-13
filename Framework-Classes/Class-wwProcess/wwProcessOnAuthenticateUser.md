This method/event is called when a user actually logs in with a username and password. 

The method receives a username and password and optionally an error message string by reference. This method should validate the user based on the username and password and return .T. or .F. in response.

This method is internally called from Authenticate() and only fired when running in `UserSecurity` or `Custom` modes, which handle the display of the login form, writing the login information into the Session object and also retrieving the cached login information from the session object. Basically overriding this method allows you to handle only the Authentication/Login operation while leaving the rest of Web Connection's User Security mechanism untouched.

### Implementing Custom Authentication/Login Validation
OnAuthenticateUser should be overridden in your own Process class. The following is the default implementation of OnAuthenticateUser which is easy to understand and override:

```foxpro
************************************************************************
*  OnAuthenticateUser
****************************************
***  Function: This method is called when a user needs to be authenticated
***            Use this to override the user authentication.
***      Pass: lcUsername     -   Username to validate
***            lcPassword     -   Password to validate
***            @lcErrorMsg    -   Error message to be set when auth fails
***    Return: .T. on success, .F. on Failure - should also set lcErrorMsg
************************************************************************
FUNCTION OnAuthenticateUser(lcUserName,lcPassword,lcErrorMsg)

*** THIS IS THE DEFAULT IMPLEMENTATION 
*** To override behavior override this method
IF EMPTY(lcUserName)
   lcUserName = ""
ENDIF 
IF EMPTY(lcPassword)
   lcPassword = ""
ENDIF

this.oUserSecurity = CREATEOBJECT(this.cAuthenticationUserSecurityClass)

*** Default implementation is not case sensitive
IF !this.oUserSecurity.Authenticate(LOWER(lcUserName),LOWER(lcPassword))
	*** Set lcErrorMsg to pass back via REF parm
	lcErrorMsg = this.oUserSecurity.cErrorMsg
	RETURN .F.
ENDIF	

*** Assign the user
this.oAuthenticatedUser = this.oUserSecurity.oUser
this.cAuthenticateUser = this.oAuthenticatedUser.UserName
this.cAuthenticateName = this.oAuthenticatedUser.FullName

Session.SetSessionVar(this.cAuthenticationUserSecurityKey,lcUsername)
RETURN .T.
ENDFUNC
*   OnAuthenticateUser
```

The method receives a username and password which your code should validate The method must return .T. or .F. to indicate whether the login operation succeeded or not. An optional @lcErrorMsg parameter is passed in which should be set to any error message that describes why a login failed.

### Custom Implementation
A custom implementation might do a few things differently like use a custom business object for authentication and then store some commonly used values in Session Variables and local properties:

```foxpro
FUNCTION OnAuthenticateUser(lcUsername, lcPassword, lcErrorMsg)
LOCAL loUser

loUser =  CREATEOBJECT("ttUser")

*** This is not actually derived from user security but basic structure
*** is similar. Save/Load methods.
THIS.oUserSecurity = loUser

IF !loUser.AuthenticateAndLoad(lcUserName, lcPassword)
   this.cAuthenticatedUser = ""
   this.nAuthenticatedUserName = ""
   this.nAuthenticatedUserPk = -1
   lcErrorMsg = this.oUserSecurity.cErrorMsg 
   RETURN .F.
ENDIF

*** Assign our own variables we use in the application
this.cAuthenticatedUserName = loUser.oData.FullName
this.nAuthenticatedUserPk = loUser.oData.Pk

*** Assign Session vars so we can read them back in OnAuthenticated()
Session.SetSessionVar("AuthenticatedUsername",this.cAuthenticatedUserName)
Session.SetSessionVar("AuthenticatedUserPk", this.nAuthenticatedUserPk)

RETURN .T.
ENDFUNC
```

You typically use this method in combination with [wwProcess:OnAuthenticated()](VFPS://Topic/_4FV17ESL6) which can be used after each request is validated to retrieve session values you stored when the user was originally authenticated:

```foxpro
FUNCTION OnAuthenticated()

*** Read custom Process Properties
this.cAuthenticatedUserName = Session.GetSessionVar("AuthenticatedUsername")
this.nAuthenticatedUserPk = VAL(Session.GetSessionVar("AuthenticatedUserPk"))

RETURN .T. 
```