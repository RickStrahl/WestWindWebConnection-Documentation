Security in Web Control Framework applications works the same as it does in classic Web Connection applications. The core Security mechanism is implemented through the wwUserSecurity class or by using Basic Authentication. Both can be very easily accessed through the [Process.Authenticate](vfps://Topic/_1P10UVIG9) method.

Security is connected to the relevant wwProcess class and although the wwWebPage class has an Authenticate method, that method simply forwards its processing to the wwProcess class. The Authenticate() method is a page level Authentication method which is used to intercept the page processing and forces authentication if not yet authenticated by presenting a login page.

![](IMAGES%5CWebControls%5CLoginProcessForm.png)

The form is self-contained and keeps the current URL alive. Once successful, the page posts back to the original URL which now authenticates and continues to display.

The wwProcess class handles all of this functionality through a few configuration properties and the Authenticate method. The key property values that are customizable are:

```foxpro
* ** Basic UserSecurity
cAuthenticationMode = "UserSecurity"

* ** Class used for UserSecurity style authentication
cAuthenticationUserSecurityClass = "wwUserSecurity" 

* ** A user object for the authenticated user
oUserSecurity = null

* ** The name of the user that was authenticated
cAuthenticatedUser = ""
```

The AuthenticationMode can be either Basic or UserSecurity. If Basic Windows User accounts are used through Basic Authentication provided by the Web Server. If UserSecurity is used the wwUserSecurty class is used using FoxPro based tables for the authentication store. You can specify exactly which class is used so typically you end up overriding the wwUserSecurity class and using a custom table. For example the Web Log does this:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****
DEFINE CLASS WebLogUserSecurity AS wwUserSecurity OF wwUserSecurity.prg
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****

* ** Stock Properties
cAlias = "WebLogUserSecurity"
cFilename = "Weblog\data\WeblogUserSecurity"

ENDDEFINE
* EOC WebLogUserSecurity
```

and then assigns 

```foxpro
cAuthenticationUserSecurityClass = "WebLogUserSecurity"
```

Once you've decided how to authentication you can very easily do it with code like the following inside of a Web Control Page itself:
```foxpro
FUNCTION OnLoad()

IF !Process.Authenticate()
   RETURN
ENDIF

this.lblMessage.Text = Process.cAuthenticatedUser + " " + ;
                       Process.oUserSecurity.oUser.Fullname
ENDFUNC
```

Notice that the Process class assigns the oUserSecurity member (when using UserSecurity in cAuthenticationMode), which contains some detail about the user based on the values stored in the UserSecurity table. This object is always available after authentication with values blank if authentication fails.

If you're using Basic Authentication the code changes a little bit in that you need to specify which user, group or mechanism to validate:

```foxpro
FUNCTION OnLoad()

IF !Process.Authenticate("WCINI")
   RETURN
ENDIF

this.lblMessage.Text = Process.cAuthenticatedUser 
ENDFUNC
```

Note the parameter passed to Authenticate(), which can be "ANY", "WCINI" or a user or group "ricks" or a list of users/groups "ricks,markuse". Also with Basic Authentication (Process.cAuthenticationMode="Basic") the oUserSecurity object is not available so the only information about the user that you get is the username in the Process.cAuthenticatedUser property.

### Using the wwWebLogin Control
The Page framework also includes a wwWebLogin control that can be dropped onto a page and you can use the control as a page level access validator. When not logged in the control displays as login display:

![](IMAGES%5CWebControls%5CLoginControlLogin.png)

and you can enter username and password into it. The control has a LoggedIn property you can query from the page:

```foxpro
IF !THIS.Login.LoggedIn
   this.panelAdminContent.Visible =.F.   
ENDIF
```

Based on the on the LoggedIn flag or the IsAdmin flag you can show or hide content as needed based on the users level of authentication. 

When you're logged in the control displays as a small box with the username displayed inside of it:

![](IMAGES%5CWebControls%5CLoginControlShowUser.png)

Of course you may not want to display the logged in control at all in which case you can simply hide the control altogether by setting it's visible flag to false:

Of course you may not want to display the logged in control at all in which case you can simply hide the control altogether by setting it's visible flag to false:

```foxpro
FUNCTION OnLoad()

IF !THIS.Authenticate()
   RETURN
ENDIF 

this.Login.Visible = .F.

... do whatever you need to

ENDFUNC
```

The control can also be used via code directly. For example, in the WebLog sample there’s a generic Authentication routine which looks like this:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
* WebLog_Routines :: IsAdminLogin
* *** *** *** *** *** *** *** *** *** *** *** *** ***
* **  Function: Generic Admin Security routine that can be generically
* **            called from the admin pages to validate users and force
* **            a login.
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** **
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

* ** Shut down Web Control Framework Response Object
Response.End()
RETURN .F.

ENDFUNC
```

The WebLogin class’s Login method manages all aspects of the login process from authentication to the retrieving and setting Session variables. Again, some of the behavior of the control delegates out to the Process class. 

Here based on the result of the login we display an error message in case the login fails.

All of these classes work together and behind the scenes the core functionality is based on the wwProcess class settings that drive the Session management and authentication lookups. This provides a much more flexible mechanism over WWWC 4.x for displaying login information in a variety of ways and scenarios.