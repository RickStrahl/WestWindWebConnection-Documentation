If a request Authenticated using cAuthenticationMode of *UserSecurity* you can access the user security object directly from here, with the appropriate user selected.


```foxpro
FUNCTION OnLoad()

IF !Process.Authenticate()
   RETURN
ENDIF

this.lblMessage.Text = this.cAuthenticatedUser + " " + ;
                this.oUserSecurity.oUser.Fullname)
ENDFUNC
```