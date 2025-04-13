The UserSecurity authentication model provides a FoxPro code based mechanism plus a custom HTML form for validating users. It uses the [wwUserSecurity class](VFPS://Topic/_1P30TA7N1), which provides a default implementation **using a simple FoxPro table based security implementation**. 

You can override the `wwUserSecurity` class and create custom authentication schemes that validate against your own data stores or even alternate stores like Active Directory, Azure Entra ID or another OpenId Connect/oAuth provider.

Using the [wwProcess::Authenticate()](VFPS://Topic/_1P10UVIG9) method users can authenticate against this table. The `Authenticate()` method itself contains and manages intercepting the HTTP request and displaying a login page that also validates the user info. If correct the user is forwarded to the required page - otherwise the Login page is redisplayed.

![](IMAGES/misc/logindialog.png)

### The wwUserSecurity Class
The wwUserSecurity class provides a simple class and cursor based lookup mechanism for retrieving username/password combinations. It supports creation of the table, adding and deleting of records and the familiar business object approach that Web Connection uses to hold registration data. The wwUserSecurity class is a base feature for the following enhancements.

The class is very simple and the key method is Authenticate which is passed a username and password. If Authenticate succeeds - or you call GetUser or GetUserByUserName - an internal oUser member is set with the user information. oUser contains username, password, FullName, email, admin and a notes field. It also supports Get and SetProperty methods to add additional information. 

You can extend the underlying table with new fields and these fields show up in the oUser member. You can override the table name and even override the wwUserSecurity class to customize the behavior. As long as you stick to the base method interface definition you can use subclasses for UserSecurity Authentication.

### wwProcess::Authenticate
There's a new Authenticate method on the process class that deals with Authentication. Authenticate is a one stop method meant to be a one liner in any code and provide automatic user validation based on the Authentication mode in use. The method can use either Basic/Windows Authentication or the new UserSecurity approach.

In this mode Web Connection displays a login dialog and validates the input against the table used by the UserSecurity class. 

To authenticate is as simple as this:

```foxpro
*** In the wwProcess class header
cAuthenticationMode = "UserSecurity"

*** To override the UserSecurity class specify a custom class here

cAuthenticationUserSecurityClass = "MyUserSecurity"

FUNCTION TestFunction

IF !THIS.Authenticate("ANY")
   RETURN
ENDIF

* **Access the Authenticate user name and a full UserSecurity instance
this.StandardPage("You've Authenticated as " + this.cAuthenticatedUser + "  " + ;
                  "Full User name: " + this.oUserSecurity.oUser.FullName)

ENDFUNC
```

Here's what the authentication request looks like:

![](IMAGES/misc/logindialog.png)

> ### @icon-info Customize the Sign In Form
> The login form by default is loaded from a `views\_login.wcs` and `views\_layoutpage.wcs` template that you can customize to fit your application style.

### User Data Storage
In order for this to work you need to add usernames and passwords and any additional information such as Admin status and fullname into a `UserSecurity.dbf` table (you can change the name). Web Connection then validates against this table. 

The validation process is driven through the UserSecurity class and you can customize how this class is created an used. You can override this class to provide custom functionality and user validation as long as you maintain the public interface of the base class.


There a are a few new properties on the wwProcess class that provide for Authentication functionality:

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

You can specify the Authentication Mode (Basic, UserSecurity), the name of the authentication class if using UserSecurity (defaulting to the stock wwUserSecurity using a Fox UserSecurity table). After Authentication is successful the cAuthenticatedUser property is set to the user name and you can optionally access the oUserSecurity object including it's oUser member that contains the user details (username, password, name, admin flag etc.).

The idea is that you can customize the operation of Authentication on your custom Process class. You can override the class if necessary to use custom tables or even override the functional behavior. For example the WebLog sample does the following in its custom Process class:

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

Once set up like this any calls the wwProcess::Authenticate automatically use these new settings.

To use the Process in Web Control Pages is very easy as well. Remember that the Process class is always available as an instance variable Process. So you can do the following in the Page_Load for example:

```foxpro
FUNCTION OnLoad()

IF !Process.Authenticate()
   RETURN
ENDIF

this.lblMessage.Text = Process.cAuthenticatedUser + " " + ;
                       Process.oUserSecurity.oUser.Fullname
ENDFUNC
```

### Overriding wwUserSecurity Behavior
You can easily override the user security class by creating your own class that inherits from user security and overrides relevant methods such as Authenticate. You can point it at custom tables and fields and generally change the default behavior. Or you can just subclass point at different files and uses as is.

**wwProcess::OnAuthenticateUser** 
This method is an easy way to hook up custom authentication when the cAuthenticationMode="UserSecurity". Basically the UI login feature calls the OnAuthenticateUser() method with a username and password parameter and you can choose how to perform the lookup. This method is the simplest way to hook up custom security if all you need to do is perform authentication.

When subclassing this method successful login attempts should set the Web Connection security session variable by invoking this code:

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

### wwWebLogin Control
The Page framework also includes a wwWebLogin control that can be dropped onto a page and you can use the control as a page level access validator. When not logged in the control displays as login display:

![](IMAGES/WebControls/LoginControlLogin.png)
 
and you can enter username and password into it. The control has a LoggedIn property you can query from the page:

```foxpro
IF !THIS.Login.LoggedIn
   this.panelAdminContent.Visible =.F.   
ENDIF
```

Based on the on the LoggedIn flag or the IsAdmin flag you can show or hide content as needed based on the users level of authentication. Once you're logged in the control displays as a tag that shows the user's login name:

![](IMAGES\WebControls\LoginControlShowUser.png)

Of course you can also hide the control from the page with:

```foxpro
this.Login.Visible = .F.
```

after Authentication() succeeded.

### wwWebLogin as a Non-Visual Component
The control can also be used via code directly. For example, in the WebLog sample there's a generic Authentication routine which looks like this:

```foxpro
* ***********************************************************************
* WebLog_Routines :: IsAdminLogin
* ***************************************
* ** Function: Generic Admin Security routine that can be generically
* **           called from the admin pages to validate users and force
* **           a login.
* **   Assume:
* **     Pass:
* **   Return:
* ***********************************************************************
FUNCTION IsAdminLogin()

loLogin = CREATEOBJECT("wwWebLogin")
loLogin.UserSecurityClass = "WebLogUserSecurity"

IF loLogin.Login() AND loLogin.IsAdmin
   RETURN .T.
ENDIF

Process.ErrorMsg("Access Denied",;
   "<blockquote>The Administrative features require that you log in first. " +;
   "Please return to main page of the Web Log form first and log on from there.<p></blockquote><hr>",,;
   3,Process.ResolveUrl("~/Default.blog#WebLogin") )

* **Shut down Web Control Framework Response Object
Response.End()
RETURN .F.
ENDFUNC
```

The WebLogin class's Login method manages all aspects of the login process from authentication to the retrieving and setting Session variables. Here based on the result of the login we display an error message in case the login fails.


The wwUserSecurity, wwProcess and wwWebLogin classes all work together to provide a common Authentication experience.