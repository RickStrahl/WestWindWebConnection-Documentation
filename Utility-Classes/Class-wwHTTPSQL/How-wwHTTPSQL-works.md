The wwHTTPSQL class works by marshalling a SQL command from the client side to the server to be executed there and returning the results back to the client side. The wwHTTPSQL class actually communicates with a matched wwHTTPSQLServer class on the server side to perform the marshalling and data formatting, so that the client app just sets a few properties to tell it where to find the server and then send a SQL command to execute. The results are returned typically as cursors but optionally you can just utilize the returned XML directly.

 All request data and responses are passed as XML strings between the client and server - the input is an XML request string that contains the SQL statement, any parameters and a few other settings, while the response typically will be either a cursor result or return value from a stored procedure. Alternately some commands might not return any data and only a confirmation will be returned as XML. Error information if it occurs also is returned in XML format.

![](IMAGES\MISC\WWHTTPSQLHOWITWORKS.JPG)

### The Client Side
The client side works through the wwHTTPSQL class which is subclassed from wwHTTP and inherits all of its HTTP functionality so you can fully configure all aspects of the HTTP connection to the server. To call a remote data source you simply specify a URL which you'll have to set up separately using a Web Connection (or any kind of Fox) backend. Then you simply call the Execute() method to fire away on the server with SQL statements or stored procedure calls.

```foxpro
DO  wwHTTPSQL  && Load Libs

oHSQL = CREATEOBJECT("wwHTTPSQL")
oHSQL.cServerUrl = "http://localhost/wconnect/wc.dll?http~httpsql"
oHSQL.nConnectTimeout = 10

oHSQL.cSQLCursor = "TDevelopers"

*** Plain SQL statements
lnCount = oHSQL.Execute("select * from wwDevRegistry")
IF oHSQL.lError
   ? oHSQL.cErrorMsg
ELSE
   BROWSE
ENDIF

WAIT window

*** Non-Result SQL Statement
? oHSQL.Execute("delete from  wwDevRegistry where pk > 200")
IF oHSQL.lError
   ? oHSQL.cErrorMsg
ELSE
   ? "records deleted"
ENDIF   

*** Calling a stored procedure
pnID = 0
pcTablename = "wwDevRegistry"
oHSQL.AddSQLParameter("pnID",pnID,,.T.)
oHSQL.AddSQLParameter("pcTableName",pcTableName)
oHSQL.AddSQLPArameter("pcIDTable","wwr_id")
oHSQL.AddSQLParameter("pcPKField","pk")

? oHSQL.Execute("Execute sp_ww_NewId 'wwdevregistry',?@pnID")

IF oHSQL.lError 
   ? oHSQL.cErrorMsg
ELSE   
   *** Grab the return value
   ? oHSQL.vReturnValue
   
   *** If a cursor also was returned
   * BROWSE
ENDIF

*** Call a Fox Method on the wwHTTPSQLServerSubclass
? oHSQL.Execute("Method RunThisOnTHeServer('Test')")
IF oHSQL.lError 
   ? oHSQL.cErrorMsg
ELSE   
   *** Grab the return value
   ? oHSQL.vReturnValue
   
   *** If a cursor also was returned
   BROWSE
ENDIF
```

To execute any plain SQL statement just use the Execute method. To execute a stored procedure on SQL Server call the procedure and pass the appropriate parameters. If you need to pass named parameters call AddSQLParameter - and the variable is marshalled from the client to the server. It will be recreated on the server and will be in scope when whatever SQL statement or command executes on the server.

Stored proedures using the Execute SQL command are supported on SQL Server. Parameters are passed either as literal values or as named parameters and they can return result values. A single returnvalue can be picked up using the vResultValue property. Parameters are returned back to you using the llReturnValue flag of the AddSQLParameter() method which are essentially treated as 'byRef' parameters and are returned as part of the SQL response.
 
Stored procedures are also supported against a VFP backend by mapping the SP call to a method on the class that handles this request. For example, to handle the call to sp_ww_newid() against a fox backend the server side application can implement a method called wwMyHTTPSQLServer::sp_ww_newid() that has the same parameters as the stored procedure and the request will be routed to this method. Fox apps might need additional information to execute passed from client to server - you can use AddSQLParameter to pass any variables from client to server. Remember, they will be created on the server. SO if you need additional information like a tablename, pknamefield etc. you can pass those as extra AddSQLParameter() calls. 

You can also execute a method on the wwHTTPSQLServer object directly using the METHOD keyword in the SQL syntax. The last example above demonstrates calling a method called RunThisOnTheServer('Test'), which actually calls a custom class method on the wwMyHTTPSQLServer class:


```foxpro
DEFINE CLASS wwHTTPSQLServer_Demo as wwHTTPSQLServer

FUNCTION RunThisOnTHeServer(lcInput)
SELECT * from wwDevRegistry WHERE company > "S" INTO CURSOR TBogus
RETURN "The result is: " + lcInput + " " + TIME()

ENDDEFINE
```

In this example, the method returns a value as well as a cursor both of which are returned as part of the remote request. Voila - Fox based stored procedures! The next section describes the server side and how this class is loaded in more detail.


### The server side
On the Web server an instance of the wwHTTPSQLServer class needs to handle the incoming Web request.  The following is a standard Web Connection request method that you can set up in any Web Connection process class:

```foxpro
* wwMywwProcess::HTTPSQL
FUNCTION HTTPSQL()

*** Check for user validation here if you chose
*IF !THIS.Login("ANY")
*	RETURN
*ENDIF

*** Create Data Object and call Server Side Execute method (wrapper for Process Method)
SET PROCEDURE TO wwHTTPSQLServer ADDITIVE  && Preferrably load once at startup! Slow this way

loDatat = CREATEOBJECT("wwHttpSqlServer")

*** You can limit allowed commands here
loData.cAllowedCommands = "select,execute,insert,update,delete,method,"

*** Create SQL Connection to use for this handler
oSQL = CREATEOBJECT("wwSQL")
oSQL.cConnectString = "server=(local);driver={SQL Server};database=wwDeveloper;pwd=sa;uid=;"
loData.oSQL = oSQL 

*** Alternately recycle a persistent SQL connection 
*loData.oSQL = Server.owwDevSQL

*** Pass XML POST data to the SQL handler
loData.S_Execute( Request.FormXML() )  

*** Create the response (XML)
Response.ContentType = "text/xml"
Response.AddForceReload()

Response.Write( loData.cResponseXML )
RETURN
```

The core of this handler consists of setting up the SQL Server connection in the oSQL member and then calling the S_Execute() method with the XML POSTED to the server by the wwHTTPSQL client. Once the handler is done an XML result is returned in the cResponseXML method and sent out into the HTTP stream back to the client.

If you're using Fox data don't set up a SQL object and the SQL will attempt to run against local Fox tables.

### Security
For security you can set up Basic Authentication on the server and use the cUserName and cPassword properties on the wwHTTPSQLClient client. Data travelling over the wire can be protected by using SSL encryption via the HTTPS:// protocol. To use this simply specify an HTTPS:// url and make sure that the Web Server is running an SSL certificate.

Finally you can protect your application by limiting the SQL commands that are allowed to pass through your server. For example you can limit commands to the SELECT statement and stored procedures to disallow any explicit access to destructive commands on your server using the cAllowedCommands property.