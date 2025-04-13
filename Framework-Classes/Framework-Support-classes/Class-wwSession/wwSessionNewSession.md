This method creates a new Session record for the current user. New sessions should be created when the session doesn't already exist. The following example demonstrates:

```foxpro
lcID = Request.GetCookie("wwDemoCookie")

loSession = CREATE("wwSession","wwDemoSession")

IF !loSession.IsValidSession(lcID)
   lcID = loSession.NewSession()

   * ** Add the cookie to the HTTP header
   Response.AddCookie("wwDemoCookie",lcId)
ENDIF


* ** Make sure the cookie gets written
Response.ContentTypeHeader(loHeader)

... more HTML here

RETURN
```

NewSession() tends to be preceeded by a call to IsValidSession() to determine whether a new session needs to be created for the user. Obviously you have more control about this code such as first going to a login page and then assigning the new session after the user logged in successfully.

### Use wwProcess::InitSession()
In most applications you don't have to write code like this - instead wwProcess::InitSession() if called from your wwProcess::OnInitProcess() method will perform this task for you and ensure that the Session object will be precreated for you.

Unless you have a very specific reason for overriding Session behavior we recommend not to do your own session handling. Use InitSession() since it provides the 99% use case without the extra code and logic to ensure that the Session gets properly initialized for each request.