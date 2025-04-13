A SQL Connect string used to connect to the database. If you use this property a new connection is created for each request.

If you want to reuse a connection use the [oSQL](vfps://Topic/wwHTTPSQLServer%3A%3AoSQL) member to assign a persistent wwSQL object instance.

This property, if left blank on the server can be set from the client. If the client sets its cSqlConnectString property the value is passed forward to this property. You can force a specific connection by providing a connection string which will override anything the client sends. To completely disallow ODBC remote access use the special value of "NOACCESS".