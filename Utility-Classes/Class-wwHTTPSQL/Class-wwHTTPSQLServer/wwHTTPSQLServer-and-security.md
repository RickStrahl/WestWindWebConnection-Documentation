Generic data handlers like wwHTTPSQLServer are always dangerous because it's basically a potential line into your database backend for your application. There are several ways to protect your application

* **Authentication**  
You can use Basic Authentication to filter access to your application. In Web Connection this is as simple as adding the following code to the top of the Process class method code shown above:
  
```foxpro
*** Check for user validation here if you chose
IF !THIS.Login("ANY")
	RETURN
ENDIF
```  

This disallows anonymous access to the request handler so that the client side must provide a valid NT username and password. On the client side the wwHTTPSQL::cUsername and cPassword properties can be used to handle this (for wwBusiness it's wwBusiness::oHTTPSQL::cUsername/cPassWord). See the [wwProcess::Login](vfps://Topic/wwProcess%3A%3ALogin) method for more details on options available.

* **Use SSL to encrypt data**  
Data that travels over the wire is not encrypted by default. If you're worried about the sensitivity of the data travelling over the wire use SSL on the server to force the data to be encrypted. Assuming you've installed a certificate on your Web Server, all you have to change is the cServerURL property on the client to *https://* to use SSL.  

* **Hide your URLs**  
This one's a little bit of common sense. Don't publish your URLs you use to access the remote data source. While this will not keep serious hackers from finding out where the data is served from it will reduce people trying to play with the data or try to retrieve data in ways that you didn't intend. Since these tools are mainly meant for client side operation in Fat client applications you can compile the server urls into the application or hide them in encrypted fields in a config file or the registry. The fewer people know the URLs the fewer people will even try to attempt to do any non-intended access to your data.

* **Limiting SQL commands that can execute**  
By default all commands are allowed against the backend, but you can limit the commands that are allowed to query commands or whatever you choose. Just add any commands that are allowed. Note that the METHOD command allows calling methods on the wwHTTPSQLServer object if you subclass and add additional methods.

* **Process and filter SQL statements **  
The examples above use the highlevel syntax for running these SQL commands. There are also low level methods that handle the parsing, execution and XML creation separately. In particular the ParseXML method takes the incoming XML and parses the data to local properties of the object. Once this is done you can check the properties for any security issues such as checking the SQL statement for access to specific tables (see example below):

```foxpro
loData.cRequestXML = Request.FormXML()

* ** Parse XML into properties
IF loData.ParseXML()
  * ** Custom ERror Checking - disallow access to WWS_ files
  * ** Here I check the SQL statement for a file prefix in a specific app
   IF ATC("WWS_", loData.cFullSQL) > 0
	* ** This creates an appropriate error XML doc
      loData.S_ReturnError("Access to table denied")
   ELSE   
      IF loData.ExecuteSQL()
         loData.CreateXML()
      ENDIF
   ENDIF
ENDIF      

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