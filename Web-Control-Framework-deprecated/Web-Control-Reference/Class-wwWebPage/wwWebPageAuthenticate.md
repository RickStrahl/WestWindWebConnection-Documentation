Allows you to check whether the current request is authenticated and if not prompt for authentication.

This mechanism is fully self contained - it will:
<ul>
* Check for authentication
* If not authenticated present an authentication dialog
* After authentication is successful return you back to the original page
</ul>

This mechanism simply defers to [wwProcess.Authenticate.](vfps://Topic/_1P10UVIG9). The authentication mechanism used (Basic or wwUserSecurity) depends on the [wwProcess.cAuthenticationMode property](vfps://Topic/_1P10UWX36) configured on your process class. The default is Basic (ie. Windows Authentication).

This simple method allows you to query the user's authentication in the OnLoad() of the page:

```foxpro
IF !THIS.Authenticate("ANY")  && Basic Auth 
	RETURN  && not validated - login dialog
ENDIF

* ** Authenticated - move on
```

Typically you'd call this code from the OnLoad of the form right at the beginning of page processing.