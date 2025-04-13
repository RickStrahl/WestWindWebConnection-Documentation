wwHTTPSQLServer is a server side handler that uses incoming XML to generate an XML response. As such it works with strings (or a DOM object) and can be used from any Visual FoxPro based Web backend.

The following is an example of the handler implementation using Web Connection:

```foxpro
* wwMywwProcess::HTTPSQL
FUNCTION HTTPSQL()

* ** Create Data Object and call Server Side Execute method (wrapper for Process Method)
SET PROCEDURE TO wwHTTPSQLServer ADDITIVE

loData = CREATEOBJECT("wwHTTPSQLServer")
loData.cConnectString = "server=(local);driver={SQL Server};database=wwDeveloper;pwd=sa;uid=;"

* ** You can limit allowed commands here
loData.cAllowedCommands = "select,execute,insert,update,delete,method,"

* ** Pass XML POST data to the SQL handler
loData.S_Execute( Request.FormXML() )  

* ** Create the response (XML)
loHeader = CREATEOBJECT("wwHTTPHeader")
loHeader.SetProtocol()
loHeader.SetContentType("text/xml")
loHeader.AddForceReload()
loHeader.AddHeader("Content-length",TRANSFORM(LEN(loData.cResponseXML)))
Response.Write( loHeader.GetOutput() )

Response.Write( loData.cResponseXML )
RETURN
```

That's all that's needed and it's rather straight forward. The client sends XML request info which is simply decoded by S_Execute after the object is created and configured for operation. In this example above a new SQL Server connection is used on each hit.

### Reusing SQL connections
You'll probably want to reuse SQL connection rather than creating a new one each time and you can do this by using a persistent wwSQL object reference that is attached to your Server object.

```foxpro
* ** In your mainline
* ** Create the object
THIS.AddObject("oMySQL",CREATEOBJECT("wwSQL"))
THIS.oMySQL.Connect("server=(local);driver={SQL Server};database=wwDeveloper;pwd=sa;uid=;")
```

Then in the method described above use this SQL object reference like this:

```foxpro
loData = CREATEOBJECT("wwHTTPSQLServer")
loData.oSQL = Server.oMySQL

loData.S_Execute( Request.FormXML() )
```

At this point the connection will be reused rather then creating a new one each time resulting in much improved performance.