Determines whether the server is running in DebugMode or 'release mode'. When this flag is set to .T. error handling is disabled and errors stop in code. When .F. errors are handled and managed to various handlers that by default display error messages. 

This flag is always accessible inside of a Web Connection applications as *Server.lDebugMode*.

This flag replaces the #define DEBUGMODE from previous versions of Web Connection.