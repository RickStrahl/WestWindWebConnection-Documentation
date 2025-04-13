If a request Authenticated using cAuthenticationMode of *UserSecurity* you can access the User Security's 


```foxpro
FUNCTION OnLoad()

IF !Process.Authenticate()
   RETURN
ENDIF

this.lblMessage.Text = this.cAuthenticatedUser + " " + ;
                this.oUserSecurity.oUser.Fullname)
ENDFUNC
```