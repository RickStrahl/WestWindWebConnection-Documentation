The wwSession class provides  session management (or said another way - user tracking) to your Web Connection applications. The wwSession class is actually fairly low level and doesn't provide for management of the Cookie or ID, but rather providers merely the mechanism for storing and retrieving session information. Using wwSession in combination with wwProcess::InitSession or wwWebPage.lEnableSessionState makes the session usage automatic.

### wwProcess::InitSession is the easiest way
The easiest way to utilize Sessions in Web Connection is by using the [wwProcess::InitSession()](vfps://Topic/_S8413MYP2)method which combines the Cookie management and Session management in a single place. If you're using the Web Control Framework setting lEnableSessionState to .T. before you call the Session object - preferrably in the class definition - has the same effect.

With one of these methods/property called the Session object becomes available as a Session variable on which you can call Session.GetSessionVar() or Session.SetSessionVar() to retrieve or set data that is persistent for a given user of your Web site.

To use InitSession simply call it in the startup code of your Process class:

```foxpro
FUNCTION OnProcessInit()
Process.InitSession("wwDemo")
Session = THIS.oSession
DODEFAULT()
RETURN
```

or you can call Process.InitSession anywhere explicitly before accessing the Session object

If you use Sessions globally in most or every request then setting up default Session handling in OnProcessInit() is preferrable because then the Session object is ALWAYS available.

If you don't use them globally and you use Web Control Framework pages you can choose to enable Session state on a per page level by setting the lEnableSessionState property in the class definition:

```foxpro
DEFINE CLASS MyWebPage : wwWebPage

lEnableSessionState = .T.

FUNCTION OnLoad()
...
ENDFUNC

ENDDEFINE
```

Once initialized the Session object becomes available on the process object as a PRIVATE Session variable.

```foxpro
FUNCTION YourProcessMethod

lcSessionVar = Session.GetSessionVar("MyVar")
IF EMPTY(lcSessionVar)
   *** Not set
   Session.SetSessionVar("MyVar","Hello from a session. Created at: " + TIME())
   lcSessionVar = THIS.oSession.GetSessionVar("MyVar")
ENDIF

THIS.StandardPage("Session Demo","Session value: " + lcSessionVar)
RETURN
```

InitSession and simply using the Session object works fine as long as you use one of the standard HTTP header routines on the Response object. See the [InitSession topic](vfps://Topic/_S8413MYP2) for more details.

### Manual Session management
In order to make wwSession more flexible it is independent of how the Session ID is managed. This makes it possible for example to use URL provided IDs instead of cookies to manage the Session Id.

With manual sessions you can determine when to assign users new sessions, such as after they have logged into some sort of logon page only. Otherwise new sessions may not be assigned. This allows you to create sessions based on application logic such as whether a user logged in.

[IsValidSession()](vfps://Topic/wwSession%3A%3AIsValidSession) is the method used to verify the existance of a session Id and this code tends to be central to an application or applet. For this reason, the Session checks and assignments tend to be made in code that runs in your subclass of the [wwProcess::Process()](vfps://Topic/wwProcess%3A%3AProcess) method. [NewSession()](vfps://Topic/wwSession%3A%3ANewSession) creates a new session for a user and you'll typically use this method following a call to IsValidSession() that failed. You can also branch off to user validating code first before then calling NewSession() to assign a new ID.

With the use of HTTP Cookies or Ids passed as part of the URL you can track people through a site. This class provides a mechanism for storing and retrieving session specific values for a particular user by creating an entry in a table that has a timeout. Sessions use a single HTTP cookie which you still have to manage manually and pass through this session object. However, with the session class this process is very simple (see code below).

Methods for creating, reading and timing out the session are provided. You can generically attach character variables to a session object, the values of which are stored in a free form memo field using the SetSessionVariable() method. The variables can be retrieved at any time with the GetSessionVariable() method. The data is stored in fragment XML format in the memo field.

<h2>How it works</h2>
The wwSession object is based on a table, which stores all the session information in its fields. The table is driven through a SessionId, which is typically driven through an HTTP cookie, but can be any mechanism you see fit to track a user. 

```foxpro
CREATE TABLE ( THIS.cDataPath+THIS.cTableName ) FREE;
  (SESSIONID    C (14),;
   USERID      C (15),;
   FIRSTON     T ,;
   LASTON      T ,;
   VARS        M ,;
   BROWSER     M ,;
   IP          M ,;
   HITS		   I )
```

The table is created the first time you try to use any of the methods of the session object - you can use the Reindex() method to clean up and purge the session file. The file can be extended with your own custom fields and you can use SetField() and GetField() to retrieve these customfield values (as well as the full content of the ones listed above) or by using Session.oData.CustomFieldName.

The SessionId is the unique identifier for each session. Using the NewSession() method a new ID is generated and an entry is created in the table. The resulting SessionId should be used as the Cookie you pass back to the browser. On the next hit you can check for this Cookie and use it to check for a valid session using the IsValidSession() method.

wwSession doesn't explicitly use the UserId field, but you can assign this field as a parameter of NewSession() and use it to cross reference a Session user with a user in an actual user table or security descriptor table. To optionally log the Browser and IP address you can assign a reference to a wwCGI object to the oCGI property. CDATA1 and CDATA2 are not used but can be used by you to store additional data without explicitly adding fields.

The following example uses a wwSession object:

```foxpro
* wwDemo :: SessionDemo
FUNCTION SessionDemo
LOCAL loSession, lcCookie, loHeader, lnCount

*** Create an instance of the session
loSession=CREATE("wwSession")
loSession.nSessionTimeout= 300   && Seconds  - 5 minutes

*** Username from the HTML form (optional)
lcName = Request.Form("txtName")

*** Retrieve the HTTP Cookie
lcCookie=Request.GetCookie("WWDEMOID")

*** We'll need to create a custom HTTP header so we can potentially
*** add the Cookie to it
loHeader=CREATE("wwHTTPHeader")
loHeader.DefaultHeader()

*** Check if we have a valid Session from our Cookie
IF !loSession.IsValidSession(lcCookie)
   *** Assign Request object to session so NewSession 
   *** can read IP and Browser and store it in table
   loSession.oRequest = Request 

   *** Nope - we have to create the session
   lcCookie=loSession.NewSession()
   
   *** And add a Cookie to the Request Header
   loHeader.AddCookie("WWDEMOID",lcCookie)
ENDIF

*** Save name and company in the Session
IF !EMPTY(lcName)
   loSession.SetSessionVar("Name",lcName)
ELSE   
   lcName = loSession.GetSessionVar("Name")
ENDIF

*** Now let's deal with our counter demo code
*** If counter doesn't exist VAL will return 0 which is OK here
lnCount = VAL( loSession.GetSessionVar("wwDemoCounter") )

lnCount = lnCount + 1

*** Note we have to pass loHeader as a parameter since we might have
*** added a cookie to it in this request.
THIS.StandardPage("Session Demo",;
                  IIF(!EMPTY(lcName),"Welcome " + lcName + "!","*** Base Class Abstract Methods") + "<p>" + CRLF + ;
                  "<b>You've hit this page: " + TRANS(lnCount) +  " times.<br>"+ CRLF +;
                  "Your Session ID is: " + loSession.cSessionId + ".</b><p>"+ CRLF +;
                  "The counter should keep increasing as you refresh this page. "+CRLF +;
                  "To start a new session start up a new instance of your browser or shut down "+CRLF +;
                  "the browser and restart it and then come back to this page. The counter will " + CRLF +;
                  "start over at 0. This session will time out after 5 minutes of inactivity.<p>"  +CRLF +; 
                  [<a href="wc.wc?wwDemo~ShowSession">Click here</a> to see the content of your session] ,;
                  loHeader)

*** Update the counter and write it back into the session
loSession.SetSessionVar("wwDemoCounter",TRANSFORM(lnCount))

RETURN
ENDFUNC
```

Typically the Session Check code and new assignment (the IsValidSession() and NewSession() block) is required on every request, so this should probably live in the your subclassed version of wwProcess::Process() including the header generation. Moving the code there makes every request in the application check for the session rather than having to perform the check in all the request methods.

This sort of code is usually not required. Note that you can specify a SessionKey, Timeout and permanent persistence in InitSession, so there really should be very few scenarios where manual session management is required.