The name of the Session Cookie used for this application. This is the default Session value.

This value defaults to the WWWC_DEFAULT_SESSIONCOOKIE_NAME defined in wconnect.h. It's recommended that if you override this value you override it in the wwProcess class definition:

```foxpro
DEFINE CLASS WebLogProcess as wwProcess

cSessionKey = "WebLogCookie"

ENDDEFINE
```