The UserSecurity authentication model provides a FoxPro code based authentication mechanism and a custom HTML form for validating users. It uses the [wwUserSecurity class](VFPS://Topic/_1P30TA7N1) which provides a default implementation **using a simple FoxPro table based security implementation** by default. 

You can override the `wwUserSecurity` class and create completely custom authentication schemes that validate against your own data stores or even alternate stores like Active Directory, Azure Entra ID or another OpenId Connect/oAuth provider.

Using the [wwProcess::Authenticate()](VFPS://Topic/_1P10UVIG9) method users can authenticate against this table. The `Authenticate()` method itself contains and manages intercepting the HTTP request and displaying a login page that also validates the user info. If authentication is correct, the user is forwarded to the required page - otherwise the Login page is re-displayed.

![](/images/misc/logindialog.png)


### The Basics!
There are two ways you can use the built in, default UserSecurity Authentication in its simplest, default form:

* Per Request
* Globally for all Requests

### Setup and Configuration
In order to use the default UserSecurity Authorization handling you need to first enable it via a property override:

```foxpro
*** Set property in your wwProcess class
cAuthenticationMode = "UserSecurity"
```

This enables the UserSecurity mode and allows you to call `THIS.Authenticate()`. It also calls implicitly calls `THIS.InitSession()` if it hasn't been explicitly called previously.

> If you need custom Session handling with a persistent session, make sure to call `THIS.InitSession()` explicitly in your Process class `OnProcessInit()` handler before `THIS.Authenticate()` is called.

#### Per Request - Process Method Handling
If you want to authenticate on a per request you can add a call to `Authenticate()` to any process method like this:

```foxpro
FUNCTION TestFunction

*** Check for any authenticated user
IF !THIS.Authenticate("ANY")
   RETURN  && not authenticated - challenges again
ENDIF

*** Logged in! Access the Authenticate user name and a full UserSecurity instance
this.StandardPage("You've Authenticated as " + this.cAuthenticatedUser + "  " + ;
                  "Full User name: " + this.oUserSecurity.oUser.FullName)
ENDFUNC
```

This works well for applications where only certain requests need to be secured. For example, you may have a few Admin requests that require logging in while most of the application can be accessed without authentication.

#### Global Authentication Handling
An alternate approach that works better for applications where most requests require authentication, is to use global authentication that consolidates the call to `Authenticate()` into the `OnProcessInit()` handler that is fired on every request:

```foxpro
FUNCTION OnProcessInit

* other OnProcessInit() code

*** Update this condition with any endpoints that
*** DON'T AUTHENTICATE or any other conditions
*** that you want that should be anonymous
lcScriptName = LOWER(JUSTFNAME(Request.GetPhysicalPath()))
llIgnoreLoginRequest =  INLIST(lcScriptName, ;
                               "default", "login")
  
IF !llIgnoreLoginRequest AND !THIS.Authenticate("any")
   RETURN .F.   
ENDIF   

RETURN .T.
ENDFUNC
```

You can create an optional condition (perhaps with a separate function if the logic is complex) to specify whether authentication should fire. 

> Note that if you choose to call `Authenticate()` on **all requests** you may also have to exclude certain requests that need explicit anonymous access like `login.myext` or `home.myext` etc. You can get `Request.GetScriptname()`

### The wwUserSecurity Class
The `wwUserSecurity` class' default implementation provides a simple class and cursor based lookup mechanism for retrieving username/password combinations. It supports creation of the table, adding and deleting of records and the familiar business object approach that Web Connection uses to hold registration data. 

The class is very simple and the key method is `Authenticate()` which is passed a username and password. If Authenticate succeeds - or you call `GetUser()` or `GetUserByUserName()` - an internal `oUser` member is set with the user information. `oUser` contains username, password, fullName, email, admin and a notes field. It also supports Get and SetProperty methods to add additional information. 

You can extend the underlying table with new fields and these fields show up in the `oUser` member. You can override the table name and even override the `wwUserSecurity` class altogether to customize the behavior. As long as you stick to the base method interface you can use subclasses for UserSecurity Authentication.

### wwProcess::Authenticate
There's a new `Authenticate()` method on the process class that deals with Authentication. `Authenticate()` is a high level method meant to be a one liner in any code and provide automatic user validation based on the Authentication mode in use. It essentially orchestrates the User Security class and setting the cookie information to persist the user data across requests, once authenticated.

> `Authenticate()` works with either Basic/Windows Authentication as well as UserSecurity approach described in this topic. When using Basic/Windows authentication the Windows logon dialog is used in lieu of the HTML based login form.

To authenticate is as simple as this:

```foxpro
*** In the wwProcess class header
cAuthenticationMode = "UserSecurity"

*** You can specify a custom class here - uses default
* cAuthenticationUserSecurityClass = "myUserSecurity"

FUNCTION TestFunction

*** Check for any authenticated user
IF !THIS.Authenticate("ANY")
   RETURN  && not authenticated - challenge again
ENDIF

*** Logged in! Access the Authenticate user name and a full UserSecurity instance
this.StandardPage("You've Authenticated as " + this.cAuthenticatedUser + "  " + ;
                  "Full User name: " + this.oUserSecurity.oUser.FullName)

ENDFUNC
```

Here's what the authentication request looks like:

![](/images/misc/logindialog.png)

> ### @icon-info-circle Customize the Sign In Form
> The login form by default is loaded from the `views\_login.wcs` and `views\_layoutpage.wcs` templates. You can customize these templates, or change the path used to point at a different template to render the Html UI.

### User Data Storage
By default user login information is stored in a `UserSecurity.dbf` file. If the file doesn't exist it's auto-created as an empty file.

The file contains fields for username and password, admin status and additional fields commonly used like firstname, lastname, company, fullname etc. You can add custom fields to this data table and it will show up on the `oUser` record.

The validation process is driven through the `wwUserSecurity` default class and you can customize how this class is created and used. You can override this class to provide custom functionality and user validation as long as you maintain the public interface of the base class.

There a are a few new properties on the `wwProcess` class that provide for Authentication functionality:

```foxpro
cAuthenticationMode = "UserSecurity"

* **Class used for UserSecurity style authentication
cAuthenticationUserSecurityClass = "wwUserSecurity"

* **A user object for the authenticated user - set when UserSecurity auth starts
* **and accessible in user code. The oUser member holds user information
oUserSecurity = null

* **The name of the user that was authenticated after Authenticate() was
* **called successfully
cAuthenticatedUser = ""
```

You can specify the Authentication Mode (`Basic`, `UserSecurity`), the name of the authentication class if using not using the default `wwUserSecurity` class with its default `UserSecurity.dbf` FoxPro table. 

After Authentication is successful the `cAuthenticatedUser` property is set to the user name and you can optionally access the `oUserSecurity` object including it's `oUser` member that contains the user details (username, password, name, admin flag etc.).

The idea is that you can customize the operation of authentication on your custom Process class. You can override the class if necessary to use custom tables or even override the functional behavior to use some other authorization mechanism to verify users. 

For example the WebLog sample does the following in its custom `wwProcess` class:

```foxpro
DEFINE CLASS WebLogProcess AS wwProcess

EnableSessionState = .T.

* **Custom Properties
oBlogConfig = null
nBlogId = 1    && for now

* **Stock Property Overrides
cAuthenticationUserSecurityClass = "WebLogUserSecurity"
cAuthenticationMode = "UserSecurity"

ENDDEFINE

DEFINE CLASS WebLogUserSecurity AS wwUserSecurity OF wwUserSecurity.prg

cAlias = "WebLogUserSecurity"
cFilename = "Weblog\data\WeblogUserSecurity"

ENDDEFINE
```

Once set up like this any calls the wwProcess::Authenticate automatically use these new settings with the alternate table and class.

### Overriding wwUserSecurity Behavior
You can easily override the user security class by creating your own class that inherits from `wwUserSecurity` and then overrides relevant methods such as `Authenticate`. You can point it at custom tables and fields and generally change the default behavior. Or you can just subclass point at different files and uses as is.

**wwProcess::OnAuthenticateUser** 
This method is an easy way to hook up custom authentication when the cAuthenticationMode="UserSecurity". Basically the UI login feature calls the OnAuthenticateUser() method with a username and password parameter and you can choose how to perform the lookup. This method is the simplest way to hook up custom security if all you need to do is perform authentication.

When sub-classing this method successful login attempts should set the Web Connection security session variable by invoking this code:

```foxpro
Session.SetSessionVar(this.cAuthenticationUserSecurityKey,lcUsername)
```

You can also *call* the OnAuthenticateUser() method to validate a user and force the Session variable and Cookie to be updated essentially forcing authentication. So if you have custom code that requires authentication you can simply do:

```foxpro
llAuth = Process.OnAuthenticateUser("rick","seekrit")
```

This applies the session cookie (if not already set) and logs the user if successful by writing the required session value.

### Global Authentication
If you want to manage on a more global level rather than at the page level, you can do that as well by hooking the Authentication into the OnProcessInit. The following example demonstrates how to authenticate any request made against an admin directory:
 
```foxpro
FUNCTION OnProcessInit
LOCAL lcParameter, lcOutFile, lcIniFile, lcOldError

* **Add a global stylesheet to common HTML generation
THIS.oResponse.cStyleSheet = this.ResolveUrl("~/westwind.css")

IF ATC("/admin/",Request.GetLogicalPath()) > 0
   IF !THIS.Authenticate()
      RETURN .F. && doesn't continue processing
   ENDIF
ENDIF

RETURN .T.
ENDFUNC
```