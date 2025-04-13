This class can be used to Execute SQL statements on a Web Server and return the results back as a cursor to the client application. Any SQL command as well as calling a method on the remote server SQL handler object (essentailly allowing remote method calls) are supported.

wwHTTPSQL works by calling a server piece that must be created with a few lines of code at a give URL. This URL must be specified on the client and implemented on the server. The server piece in turn implements the [wwHTTPSQLServer](vfps://Topic/Class%20wwHTTPSQLServer) class which handles the remote SQL request.

> ### @icon-info-circle Note
> This class is based on [wwHTTP](vfps://Topic/Class%20wwHTTP) and thus inherits all of wwHTTP's HTTP properties to allow you to configure things like username/password and proxy configuration. Typical properties you'll want to access are: nConnectTimeout, cUserName, cPassword,cHTTPProxyName,cHTTPProxyUsername, cHTTPProxyPassword


Note that the wwHTTPSQL/wwHTTPSQLServer pair is a generic processing engine and can be used independently of Web Connection. In fact the class can be used in a non-HTTP environment. For example you could use this same mechanism in a Message Queue or Event based system where the messages are passed using XML. Since both the client and server use XML for messages the transport mechanism is completely switchable.