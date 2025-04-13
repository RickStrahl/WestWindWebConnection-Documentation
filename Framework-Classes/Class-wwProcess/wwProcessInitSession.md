Sessions are used to keep a server side *session* for a particular user. They use a Cookie to track the user, and a database record to store any data you attach to the session. Sessions have a timeout when they expire and the expiration lease is renewed each time the session is accessed.

### Common Usage in OnProcessInit()
You typically call `InitSession()` in `wwProcess::OnProcessInit()` to initialize the active session for every request in a process class. 

This methods does the following:

* Creates an instance of `wwSession` (or `wwSessionSql`) 
* Exposes the `wwProcess::oSession` property
* Exposes a **PRIVATE** `Session` variable
* Sets a session cookie that holds the Session Id

### Initialize the Session Globally
In your subclass of wwProcess in the Process method add the `InitSession()` call into OnProcessInit():

```foxpro
FUNCTION OnProcessInit

*** all parms are optional
*** wwDemo Cookie, 30 minute timeout, persist cookie
THIS.InitSession("wwDemo",1800,.T.)
...
RETURN
``` 

This creates a PRIVATE `Session` variable (and also `Process.oSession`) that is scoped to the Process class and all its methods. 

### Use the Session in your code
```foxpro
FUNCTION YourProcessMethod

lcSessionVar = Session.GetSessionVar("MyVar")
IF EMPTY(lcSessionVar)
   *** Not set
   Session.SetSessionVar("MyVar","Hello from a session. Created at: " + TIME())
   lcSessionVar = Session.GetSessionVar("MyVar")
ENDIF

THIS.StandardPage("Session Demo","Session value: " + lcSessionVar)
RETURN
```
### Initialize Session for each Method
While you usually want to initialize the `Session` globally, you can also call `InitSession()` in individual process methods, only where needed. If you only have a few methods that require use of `Session`, you can call `InitSession()` in each of these methods:

```foxpro
FUNCTION MyProcessMethod()
THIS.InitSession("wwDemo",1800,.T.)
...
RETURN
```