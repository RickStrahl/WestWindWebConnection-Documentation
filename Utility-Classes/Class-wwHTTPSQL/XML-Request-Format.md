You can request data from wwHTTPSQLServer using the following XML format:

```xml
<?xml version="1.0"?>
<wwhttpsql>
	<sql>Execute sp_ww_NewId ?pcTableName,?@pnID</sql>
	<maxbuffersize>0</maxbuffersize>
	<sqlcursor>TSQLQuery</sqlcursor>
	<transportmode>2</transportmode>
	<utf8>1</utf8>
	<sqlparameters>
		<pnid type="N" return="1">0</pnid>
		<pctablename type="C">wwDevRegistry</pctablename>
		<pcidtable type="C">wwr_id</pcidtable>
		<pcpkfield type="C">pk</pcpkfield>
	</sqlparameters>
</wwhttpsql>
```
The only required parameter is *sql* which is the SQL statement to execute on the server. In the example above a stored procedure is executed, and one that contains two named parameters, which are specified in the *sqlparameters* section. For SQL Server commands are executed on the Web Server using SQL Passthrough so SQL Passthrough parameter syntax is used to specify parameters. With Fox data you can simply provide the variable names without the preceeding ? character. Variables are 'marshaled' to the Web server and re-created there, so you can use any SQL parameters as you would any variables in the appropriate SQL statements.

Transportmode specifies how the data travels over the wire. 0 or not specified is the default and sends XML from the server. 2 sends an EncodeDBF() encoded cursor, but only if a cursor is returned. All other responses still are returned as XML.

The UTF8 value should be set to 0 (or not specified) if you're using a wwXML or MSXML client for the data. It should be set to 1 when you're sending the data to a browser for display or other client that expects a fully encoded XML UTF-8 string.