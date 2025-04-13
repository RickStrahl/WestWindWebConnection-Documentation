Specifies the Session timeout in minutes when you check the **Remember Me** checkbox on the authentication dialog. The default is 5 days.

This value is specified in minutes so you can specify this value in `OnProcessInit()` or you can override the property:

```foxpro
DEFINE CLASS MyProcess as WWWC_PROCESS

*** Override auth timeout
nAuthenticationTimeoutSeconds = 60 * 60 * 24 * 10   && 10 days

... 
ENDEFINE
```