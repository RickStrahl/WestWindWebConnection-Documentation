On the last page of the Wizard click Finish to create the database. If you encounter any connection problems the SQL Connection will prompt you for logon information. If the logon fails an error message will be provided.

Once the creation has completed you need to configure Web Connection properly to use the newly created SQL tables. 

### Add a SQL Object to your server's mainline code
If you have a project that was created prior to version 3.30 you need to add the following block to your mainline server program which will usually be named <yourproject>Main.prg:

```foxpro
#IF WWC_USE_SQL_SYSTEMFILES
	SET PROCEDURE TO wwSessionSQL ADDIT
	THIS.oSQL = CREATE("wwSQL")
	IF !THIS.oSQL.Connect(THIS.oConfig.cSQLConnectString)
	   MESSAGEBOX("Couldn't connect to system SQL Service. Check your SQL Connect string",48,"Web Connection")
	   CANCEL
    ENDIF
#ENDIF	
```

This block establishes a persistent connection with the SQL Server. 

> #### @icon-info-circle Tip for existing SQL Server users
>If you already use some other mechanism to manage a SQL connection and stored the Web Connection system files into this database, you can create the SQL object and rather than connect to it, set the nSQLHandle property. Although not required you should also set the cConnectString property so in case of a connection failure the wwSQL object can retry the connection.</blockquote>

### Set the SQLConnectString property in your server's INI file
Finally, the SQL connection needs a connect string in order to connect to the database. You need to use a full connection string or DSN definition. As shown above Web Connection reads this value from the server's INI file via the server's Config object. The config object persists its data in the server's INI file (<yourproject>.INI in the [Main] section) .

```foxpro
Sqlconnectstring=driver={SQL Server};server=(local);database=WestWindTest;uid=sa;pwd=
```

If you prefer to not store a configuration string in the INI file you can also hardcode the string in the SetServerEnvironment code above instead of reading it from the Server.oConfig object.

### Set the `WWC_USE_SQL_SYSTEMFILES` constant in `wconnect_override.h`
In order to for Web Connection use the object set up in the block above you need to set the following switch in `wconnect_override.h`:

```foxpro
#UNDEFINE WWC_USE_SQL_SYSTEMFILES
#DEFINE WWC_USE_SQL_SYSTEMFILES    .T.
```

> `wconnect_override.h` lets you make changes to settings in `wconnect.h` without having those changes over written when Web Connection is updated and `wconnect.h` is overwritten.  Using `wconnect_override.h` makes changes to settings independently while still getting loaded the same as `wconnect.h` settings.

This flag is used in several places in the Web Connection framework that deal with logging and the session. In particular the following places are affected:

* The logging functions in wwServer
* The InitSession method in wwProcess

### Fix any manual Session object usage (not using InitSession())
The wwServer and logging is fully self-contained. Session access is self-contained only if you use the wwProcess::InitSession method to set up sessions. If your applications use manual wwSession objects you'll have to adjust your instantiation code slightly to accomodate the wwSQLSession object:

```foxpro
#IF WWC_USE_SQL_SYSTEMFILES
   THIS.oSession=CREATE([WWC_SQLSESSION])
   THIS.oSession.oSQL = THIS.oServer.oSQL
#ELSE   
   THIS.oSession=CREATE([WWC_SESSION])
#ENDIF
```

The oSQL property is persistent and is reused on all hits and used to perform all SQL Execute commands performed over a SQL Passthrough connection to the SQL Server.

Make sure you recompile everything after making these changes so the change of the `WWC_SQL_SYSTEMFILES` flag is properly applied.

Once you've done so, all of your logging information will go to the `wwRequestLog` table and the Session data will go into `wwSession` table in the database you specified using the Wizard.