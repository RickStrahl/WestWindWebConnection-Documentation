The wwWebLogin control provides both a visual login control as well as the ability to authenticate users against a class based interface. 

### Based on wwUserSecurity for Authentication
The class based interface by default uses the wwUserSecurity class which provides a DBF based security lookup mechanism. You can override the class by setting the UserSecurityClass property with a subclass of the wwUserSecurity class. 

You can specify a class that serves as your security interface - generally you will create a custom subclass of wwUserSecurity for your particular application. For example the Web Log implements the following:

```foxpro
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****
DEFINE CLASS WebLogUserSecurity AS wwUserSecurity OF wwUserSecurity.prg
* *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** *** ****

* ** Stock Properties
cAlias = "WebUserSecurity"
cFilename = "Weblog\data\WeblogUserSecurity"

ENDDEFINE
* EOC WebLogUserSecurity
```

All this subclass does is provide a mapping to a specific user table. This class is then referenced on the control that is embedded into the Web form:

```html
<ww:wwWebLogin ID="Login" runat="server" UserSecurityClass="WebLogUserSecurity" Center="true" 
               UserMessage="<small><i>Use test/test to access the Administration features.</i></small>" 
               Width="170px" TextBoxWidth="90px" Cellpadding="2" />
```

Once this control is on a form you can click the button and it will automatically authenticate the user and if successful store the user's username in the Session object:

![](IMAGES%5CWebControls%5CLoginControlLogin.png)

Once authenticated the control displays the user credentials as a small in place tag to indicate the user is logged in:

![](IMAGES%5CWebControls%5CLoginControlShowUser.png)

### Using the Control continously
The easiest way to use this control is to place it in a central place, such as on a toolbar or sidebar panel so that you can always access the control. For example in the WebLog sample the Login control lives on the Sidebar that is displayed on every front end form. That way the application has easy access to the control and its properties to check for user authentication.

For example, to check if a user is logged in you can do:

```foxpro
IF !this.WebLogSideBar.Login.LoggedIn
   * ** Hide Delete Option
   this.hypRemove.Visible = .f.
ENDIF
```

### Single Page Logins
As an alternative you can do your user validation on a single page - say Login.wcsx and then simply use the SessionVariables to check the status of the login. To facilitate how the control writes the Session Var there's a property called LoginSessionVar which defaults to *_LoginName* (but is overridable - not recommended though as this name matches the Process Loginname). You can then use this SessionVar to check if the user is logged in anywhere in the application - the value for the variable contains the users login name.

```foxpro
lcUser = Session.GetSessionVar("_LoginName")
IF EMPTY(lcUser)
    Response.Redirect("Login.wcsx")  && your page that hosts the login control
    RETURN
ENDIF

... allow access to code here
```

If you end up doing this sort of thing in a lot of pages of your code I suggest you create a generic application specific UDF() that wraps this logic into a single validation step...

### Using Custom Security Providers
The default security provider is wwUserSecurity and it uses a default security table *UserSecurity.dbf* which is stored in the Tools directory by default. As mentioned the easiest way to extend the functionality is by implementing a class based on wwUserSecurity and then telling the wwWebLogin control to use that class for authentication.

If you decide you can't live with wwUserSecurity, your other option is to subclass the wwWebLogin control and override the Login and Logut methods. The methods are very simple and the key of them is to set the SessionVars and the LoggedIn and IsAdmin properties. You can easily plug your own classes into this simple logic as needed.