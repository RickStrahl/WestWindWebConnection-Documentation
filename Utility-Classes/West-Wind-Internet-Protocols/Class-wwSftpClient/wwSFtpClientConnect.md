This establishes the initial connection to the server. The connection needs to stay open for any of the FTP operation commands.

> You should always check the return value from the connect request and check `.cErrorMsg` for error results. 

Call `.Close()` when you're done with the connection.