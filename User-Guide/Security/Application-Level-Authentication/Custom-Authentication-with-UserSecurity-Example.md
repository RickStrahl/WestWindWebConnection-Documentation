Web Connection's user security implementation allows a lot of customization including taking over the user validation, displaying the form and for handling requests after a user has logged in by way of a few events that let you replace small chunks or the entire process of Authentication.

The following is a sample implementation that completely customizes the authentication pipeline to demonstrate each of the hooks you can plug into. Please note that you can choose which parts to override - you can override just validation and use the rest of the UI, override the UI and use the default validation logic, or completely override everything and do your own thing.

The following example uses a custom form login:

![](IMAGES/misc/CustomUserSecurityLogin.png)

Let's get started.

### Set up UserSecurity
First you need to specify that you want to use custom usersecurity authentication by setting the cAuthenticationMode to *UserSecurity*.

```foxpro
DEFINE CLASS MyProcess as wwProcess

cAuthenticationMode = "UserSecurity"
...

ENDDEFINE
```

### Authentication Properties
wwProcess exposes a few authentication properties that are set by default:

```foxpro
*** Basic UserSecurity mode: Basic or UserSecurity
cAuthenticationMode = "UserSecurity"

*** Class used for UserSecurity style authentication
cAuthenticationUserSecurityClass = "wwUserSecurity"

*** The name of the Session variable used to hold authentication data
cAuthenticationUserSecurityKey = "_AuthenticatedUser"


*** Flag that determines whether the user was authenticated
lIsAuthenticated = .f.

*** The user name/id/email of the user that was authenticated
cAuthenticatedUser = ""

*** The display name for the user (if overridden and set)
cAuthenticatedName = ""
```

You can override the first 3 properties to affect security behavior. The last 3 properties are set after authentication has succeeded.

### Authentication Manipulation Events
There are a number of events that are fired as part of Authentication that you can override to handle behavior:

* **OnAuthenticateUser**  
Override to handle the actual authentication of a username and password and return .T. or .F. Often this is all you need to do to hook custom authentication into your app. 

* **OnAuthenticated**  
This method is called after a successful login and you can use this to read or set additional session variables.

* **OnShowAuthenticationForm**  
This method lets you override the rendering of the Authentication form.

All of these methods are driven by the `.Authenticate` method which is fairly complex. While you can override `Authenticate` in its entirety it's usually easier and more modular to just override the event methods.

### Add Session Support
The first step is to apply Session support to the application since UserSecurity authentication uses sessions to track users. In this example I use global session support for this process class, but you can also isolate the Session support just for the authenticated requests in the application.

The most common place to handle this is in the `OnProcessInit()` method:

```foxpro
FUNCTION OnProcessInit
THIS.InitSession("wwMyApp")
...
ENDFUNC
```

### Force Authentication to Specific Requests
Next you need to figure out which requests in your application require Authentication. You can do this globally or on a per request basis. In either case you call the wwProcess::Authenticate() method to check for authentication.

The most common way is to identify a path in the application and based on that path force the authentication in OnProcessInit():

```foxpro
FUNCTION OnProcessInit

THIS.InitSession("wwMyApp")

*** Allow access to specific requests w/o auth
lcScriptName = JUSTFNAME(Request.GetPhysicalPath())
llIgnoreLoginRequest = INLIST(lcScriptName,"default","login","logout")

IF !THIS.Authenticate("ANY","",llIgnoreLoginRequest) 
   IF !llIgnoreLoginRequest
      *** this forces an authentiction dialog
	  RETURN .F.
   ENDIF
ENDIF

RETURN .T.
ENDFUNC
```

It's also possible to explicitly defer authentication to specific requests. Rather than globally filtering as shown above, you can just add the Authenticate() calls to individual methods directly:

```foxpro
FUNCTION MyOperation
     IF !THIS.Authenticate("ANY")
         RETURN .F.
     ENDIF
     
     * **Authenticated - go on processing
     ...
ENDFUNC
```

The code as written should work using the stock authentication display and user authorization by showing the default login dialog.

What follows is the customization to override the authentication behavior as well as the user interface.

### Overriding Authentication Logic
The default authentication behavior is handled by the OnAuthenticateUser() method which is passed username and password and by reference error message parameter. 

This method is internally called to validate the username and password. If you prefer to use your own authentication mechanism from your own business objects, or from Active Directory or other source this is where you can hook it up.

Note: you only need to do this if you want to hook in your own authentication logic. If you don't override this method you get the stock wwUserSecurity login functionality.

A custom implementation might look like this:

```foxpro
FUNCTION OnAuthenticateUser(lcUsername, lcPassword, lcErrorMsg)
LOCAL loUser

loUser =  CREATEOBJECT("ttUser")

*** This is not actually derived from user security but basic structure
*** is similar. Save/Load methods.
THIS.oUserSecurity = loUser

IF !loUser.AuthenticateAndLoad(lcUserName, lcPassword)
   this.cAuthenticatedUser = ""
   this.cAuthenticatedName = ""

   *** Custom var we track
   this.nAuthenticatedUserPk = -1
   
   lcErrorMsg = this.oUserSecurity.cErrorMsg 
   RETURN .F.
ENDIF

*** Stock variables - auto assigned to session vars
this.cAuthenticatedUser = lcUsername
this.cAuthenticatedName = loUser.oData.UserName

*** Custom variables we need to track ourselves
this.nAuthenticatedUserPk = loUser.oData.Pk
Session.SetSessionVar("AuthenticatedUserPk", this.nAuthenticatedUserPk)

RETURN .T.
ENDFUNC
```

Note that I'm setting session variable for the custom value I'm tracking which is the PK of the user while the UserId and Name are automatically tracked just by the property names on the `wwProcess` instance.

### Capturing Authentication Data on each Request
Once you are authenticated all subsequent requests just go through with the authentication set. Specifically the `Process.cAuthenticateUser`, `Process.cAuthenticatedName` and `Process.lIsAuthenticated` properties are set. This may be all you need to ensure your app is properly logged in so the following event is optional.

**If** you need to store additional information about each authenticated user, you can get notified whenever the user is authenticated on each request via the  `OnAuthenticated` event. 

The following code demonstrates retrieving Session variables and optionally saving them to custom properties on for easier access in process methods:

```foxpro
FUNCTION OnAuthenticated()

*** Read any custom auth Properties from Session
this.nAuthenticatedUserPk = VAL(Session.GetSessionVar("AuthenticatedUserPk"))

RETURN .T.
```

### Customizing the Login Dialog Display
The default login dialog provides username and password fields in a very basic login form which is based on a template stored in `views\_login.wcs`. You can customize this application specific template which is the easiest way to customize the layout.

If you want to completely customize the display you can override the `OnShowAuthenticationForm()` event which is responsible showing the form that the user sees for entering credentials.

The form you display has to return the following form variables:

* WebLogin_txtUsername
* WebLogin_txtPassword
* WebLogin_chkRememberMe (not used currrently)

The following example uses a custom script page to display login information: 

```foxpro
FUNCTION OnShowAuthenticationForm(lcUsername, lcErrorMsg)

pcUsername = lcUsername
pcPassword = ""
pcRedirectUrl = ""
pcErrorMessage = lcErrorMsg
IF EMPTY(pcUsername)
  pcUserName = ""
ENDIF

Response.ExpandScript("~\views\_login.wcs")
ENDFUNC
```

This code could be anything that generates HTML as long as it contains an HTML form that posts back the WebLogin_ form variables when submitted. 

Note that the default login form is login.wcs which I use here and which is also used by the login page (next section) so the same form is used in both cases. I can simply modify the 'standard' login template to match whatever I need it to look like.

The important thing to remember though is that it doesn't matter how you generate the HTML - just that you do and that you likely want to use the same HTML when you render the plain Login page when the user explicitly accesses that page.

### Create the Login Page Logic and HTML
Next you need to implement the actual Login and Logout request Process method handlers, so that your users can click on a link to login and logout. Here are those two simple handlers:

```foxpro
FUNCTION Login()

pcErrorMessage = ""
IF Request.IsPostback()
   pcUsername = Request.Form("WebLogin_txtUsername")
   IF this.Authenticate("ANY",@pcErrorMessage)
      Response.Redirect("~/default.ttk")
   ENDIF
   
   *** Authenticate renders the login form if we failed
   *** using OnShowAuthenticationForm() so just return
   RETURN
ENDIF

*** Only render this on a GET operation
*** Recommend you call back to the form rendering
THIS.OnShowAuthenticationForm()
ENDFUNC


FUNCTION Logout()

*** Delegate logout - clears session and properties
this.Authenticate("Logout") 

Response.Redirect("default.ttk")

ENDFUNC
```

The HTML for the the form you are using can be anything you choose. For the sample above the relative form markup might look like this:

![](IMAGES/misc/CustomUserSecurityLogin.png)

To get an idea what layout to use you can use the `views\_login.wcs` page as a starting point.


### Not everything has to be overridden
So that's what's required. This sounds fairly complicated, but most of this code is really boilerplate and only required if you want to override everything. You can choose what to override.

Maybe you only to hook in a custom user validation scheme - implement on `OnAuthenticateUser`. 

If you want to hook up custom variables that store user info you can do that by overriding `OnAuthenticateUser` to store the values into session, and then `OnAuthenticated` to retrieve those values out.

If you only want to override the display implement `OnShowAuthenticationForm`. 

There are lots of options, for default processing to minimal overriding to taking over the whole process.  Note that `wwProcess::Authenticate` is the method that drives this entire process which fires these events. If you want even more control you can take over that entire method as well.

### Complete Code Sample
For completeness sake I'm providing all the code described for the custom implementation described in this page as a single block of code for easier reference and using it as a template to work with.

```foxpro
FUNCTION OnProcessInit
LOCAL lcScriptName, llForceLogin, llIgnoreLoginRequest

THIS.InitSession("ttw")

lcScriptName = LOWER(JUSTFNAME(Request.GetPhysicalPath()))
llIgnoreLoginRequest = INLIST(lcScriptName,"default","login","logout")

IF !THIS.Authenticate("any","",llIgnoreLoginRequest) 
   IF !llIgnoreLoginRequest
	  RETURN .F.
   ENDIF
ENDIF

Response.Encoding = "UTF8"
Request.lUtf8Encoding = .T.

RETURN .T.
ENDFUNC


************************************************************************
*  Login
****************************************
***  Function: Handles display of the login form if directly accessed
***            and routes postbacks to the Authenticate() method.
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION Login()

pcErrorMessage = ""
IF Request.IsPostback()
   pcUsername = Request.Form("WebLogin_txtUsername")
   IF this.Authenticate("ANY",@pcErrorMessage)
      Response.Redirect("~/default.ttk")
   ENDIF
ENDIF

Response.ExpandScript("~\views\_login.wcs")
ENDFUNC
*   Login

************************************************************************
*  Logout
****************************************
***  Function: Just a routing mechanism to allow logging out.
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION Logout()
this.Authenticate("Logout")
Response.Redirect("~\default.ttk")
ENDFUNC
*   Logout

************************************************************************
*  OnShowAuthenticationForm
****************************************
***  Function: Show the authentication form. In this case it's simply
***            showing the application template.
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION OnShowAuthenticationForm(lcUsername, lcErrorMsg)

pcUsername = lcUsername
pcPassword = ""
pcRedirectUrl = ""
pcErrorMessage = lcErrorMsg
pcDisplayMessage = "No account? No problem - <a href='Profile.ttk'>Create one</a>"
IF EMPTY(pcUsername)
  pcUserName = ""
ENDIF

Response.ExpandScript("~\views\_login.wcs")
ENDFUNC
*   OnShowAuthenticationForm

************************************************************************
*  OnAuthenticated
****************************************
***  Function: Called after a user has authenticated so we can read
***            values out of the session.
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION OnAuthenticated()

*** Read custom Process Properties from Session which should be 
*** there since they were created when user originally signed in
this.cAuthenticatedUserName = Session.GetSessionVar("AuthenticatedUserName")
this.nAuthenticatedUserPk = VAL(Session.GetSessionVar("AuthenticatedUserPk"))

RETURN .T.   
ENDFUNC
*   OnAuthenticated

************************************************************************
*  OnAuthenticateUser
****************************************
***  Function: Handles validating username and password for the user
***    Assume:
***      Pass:
***    Return:
************************************************************************
FUNCTION OnAuthenticateUser(lcUsername, lcPassword, lcErrorMsg)
LOCAL loUser

loUser =  CREATEOBJECT("ttUser")

*** This is not actually derived from user security but basic structure
*** is similar. Save/Load methods.
THIS.oUserSecurity = loUser

IF !loUser.AuthenticateAndLoad(lcUserName, lcPassword)
   this.cAuthenticatedUser = ""
   this.cAuthenticatedUserName = ""
   this.nAuthenticatedUserPk = -1
   lcErrorMsg = this.oUserSecurity.cErrorMsg 
   RETURN .F.
ENDIF

*** Assign our own variables we use in the application
this.cAuthenticatedUserName = loUser.oData.UserName
this.nAuthenticatedUserPk = loUser.oData.Pk

*** Assign Session vars so we can read them back in OnAuthenticated()
Session.SetSessionVar("AuthenticatedUserName",this.cAuthenticatedUserName)
Session.SetSessionVar("AuthenticatedUserPk", this.nAuthenticatedUserPk)

RETURN .T.
ENDFUNC
*   OnAuthenticateUser
```