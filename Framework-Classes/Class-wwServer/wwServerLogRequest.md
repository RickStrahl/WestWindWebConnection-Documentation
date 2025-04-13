Used to log request data into the logging database. Called internally to log requests when request logging is enabled and also used to log errors when errors occur.

You can manually call this method to explicitly log items into the log. Use the first parameter to specify the 'log item detail' for the request.

In Web Connection wwProcess Code you can use:

```foxpro
Server.LogRequest("Reindexed datafiles")
```

From inside the wwServer startup code:

```foxpro
Server.LogRequest("Failed to initialize SQL Connection","",0,.t.)
```