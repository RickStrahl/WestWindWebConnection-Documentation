wwHTTPSQLServer is a generic processing class and can work with any Visual FoxPro backend. One of the alternate ways to use it is as an ASP COM component. In order to do so you can create a small wrapper class that can be called from an ASP. This wrapper just implments the basic COM functionality during initiialization and some custom error handling.

<pre>
SET PROCEDURE TO wwHTTPSQLServer ADDIT

* ** Redefine this class so it's OLEPUBLIC
DEFINE CLASS wwASPHTTPSQLServer AS wwHTTPSQLServer OLEPUBLIC

cAppStartPath = ""

FUNCTION INIT

* ** Make all required environment settings here
* ** KEEP IT SIMPLE: Remember your object is created
* **                 on EVERY ASP page hit!
SET RESOURCE OFF  && best to do in compiled-in CONFIG.FPW

SET EXCLUSIVE OFF
SET REPROCESS TO 2 SECONDS
SET CPDIALOG OFF

SET DELETED ON
SET EXACT OFF
SET SAFETY OFF

* ** Retrieve the DLL location and grab just the path to save!
THIS.cAppStartPath = ADDBS( JustPath(Application.ServerName) )

* ** Add the startup path to the path list!
* ** Add any additional relative paths as required
SET PATH TO ( THIS.cAppStartpath )

* ** Call back to the original Constructor
DODEFAULT()

ENDFUNC

FUNCTION ERROR
LPARAMETER nError, cMethod, nLine
THIS.lError = .T.
THIS.cErrorMsg= THIS.cErrorMsg + "<BR>Error No: " + STR(nError) + "<BR>  Method: " + cMethod + "<BR>  LineNo: " +STR(nLine) + "<BR>  Message: "+ message() + Message(1) + "<HR>"
ENDFUNC

ENDDEFINE</pre>

Add this program file to a project and compile it into an multi-thread COM  (MTDLL) component. Here I create wwASPHTTPSQLServer.dll. You should also add this new component into COM+. The purpose for this is to allow you to configure the security context of the component so it can access your data files and SQL Server connections. Add the component and set the Impersonation to a user that has rights to access your database files and/or connections.

Once that's done, you can create an ASP page that serves as the server side handler:

<pre><%
Response.ContentType = "text/xml"
Response.Expires=0
 
'* ** Get the XML input
'set oXML = Server.CreateObject("MSXML2.DOMDOCUMENT")
set oXML = Server.CreateObject("MSXML2.FreeThreadedDOMDocument")

oXML.Load(Request)

set loData = Server.CreateObject("wwASPHTTPSQLSERVER.wwASPHTTPSQLServer")
loData.cConnectString = "server=(local);driver={SQL Server};database=wwDeveloper;pwd=sa;uid=;"
loData.S_Execute(oXML.XML) 

'Response.Write(loData.CERRORMSG) ' debug
Response.Write(loData.cResponseXML)

%>
</pre>

From here on out the behavior of your server application should be identical from using a Web Connection server for the data access. Just change the cServerUrl property to the appropriate URL that points at your ASP page.