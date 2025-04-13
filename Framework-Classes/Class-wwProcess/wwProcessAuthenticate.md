Authenticates a user based on the Authentication method specified in the [cAuthentionMode](vfps://Topic/_1P10UWX36). The method handles the entire Web process of Authentication and tracking a user via a Session (with an HTTP Cookie). Authentication modes supported are *Basic*, *UserSecurity* or <Custom>. UserSecurity uses the wwUserSecurity class for authentication. Custom requires overriding the [OnAuthenticateUser](vfps://Topic/_2LH0UZF0J) method which allows a custom authentication business process. The default is Basic which uses HTTP Basic Authentication.

The Authenticate method provides a comprehensive authentication hook to a Web Connection request and check for authentication easily from within your code. It allows using Basic Auth, the UserSecurity class, or a custom implementation based on username and password for authentication. This method is always accessible with Process.Authenticate() or alternately in Web Page code as THIS.Authenticate() (ie. on the wwWebPage class) which simply forwards to Process.Authenticate.

If Authentication succeeds the [Process.cAuthenticatedUser property](vfps://Topic/_1P10VE2RL) is set which you can check for the username that is authenticated in your code.

### Basic Authentication
With Basic Auth all you need to provide is the authentication directive/user name. Call Authenticate with a parameter of ANY or WCINI or a username list (comma delimited) to authenticate for that specific user. Leave blank and authentication will always succeed - no authentication occurs. Basic Authentication works against Windows User Accounts and is managed by the Web Server itself.

The request fires and if not authenticates pops up a Windows Authentication box. You put in your username a

### UserSecurity Authentication
This mechanism provides more control and uses a FoxPro class and a FoxPro table (by default) to authenticate users. You can optionally specify which class is used (must follow the wwUserSecurity interface or inherit from it) to authenticate, which allows overriding default behavior and operation of the class. The default mechanism looks up user info in a FoxPro table.

When authentication succeeds you can check the cAuthenticatedUser property which returns the user's login name. You can also access the oUserSecurity property to get full access to the currently selected user (Process.oUserSecurity.oUser) but note that this requires a database lookup which otherwise is performed only when logging in.

User Security Authentication stores authentication info in Session Variable which also means that Cookies must be enabled for this feature to work.

### Custom Authentication
Works the same as UserSecurity except that you can override the [OnAuthenticateUser()](vfps://Topic/_2LH0UZF0J) method of the wwProcess class. This method receives username and password and an out error message parameter. You can then implement your own business logic to authenticate the user based on these simple values.