This method is responsible for verifying a session id. It looks for the ID in the Session table and if found returns .T. other .F. It also loads the oData member with the content of the session record.

The logic typically goes like this:

```foxpro
lcSessionId = Request.GetCookie(lcSessionCookieName)

THIS.oSession=CREATEOBJECT([WWC_SESSION])

* ** Check for Session or create a new one
IF !THIS.oSession.IsValidSession(lcSessionId)
   * ** Also log the browser and RemoteAddress - assign oRequest property
   THIS.oSession.oRequest=THIS.oRequest    

   * ** Nope create it - 
   * ** optionally use existing sessionid retrieved from cookie 
   * ** to allow persistant user session ids
   lcSessionId=THIS.oSession.NewSession(,IIF(llPersist,lcSessionId,"") )

   * ** Web Control Page
   * ** Force HTML object to add the Cookie in Header creation
   Response.AddCookie(lcSessionCookieName,lcSessionId,"/",;
                            IIF(llPersist,"NEVER",""),"",.T.)
ENDIF

* ** Update PRIVATE Session var if it previously exists
Session = this.oSession

RETURN lcSessionId
```


If IsValidSession() returns .F. you typically will want to create a new session and also add a Cookie (or other tracking mechanism) with the newly generated session id.

IsValidSession calls the lower level LocateSession() and then sets the LastOn, Hits and SessionId fields of the oData member. You should not call LocateSession directly unless you don't need these update operations.

### Use wwProcess::InitSession()
In most applications you don't have to write code like this - instead wwProcess::InitSession() if called from your wwProcess::OnInitProcess() method will perform this task for you and ensure that the Session object will be precreated for you.

Unless you have a very specific reason for overriding Session behavior we recommend not to do your own session handling. Use InitSession() since it provides the 99% use case without the extra code and logic to ensure that the Session gets properly initialized for each request.