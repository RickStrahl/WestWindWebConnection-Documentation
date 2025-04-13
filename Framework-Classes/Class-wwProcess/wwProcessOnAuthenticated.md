Event called after a user has been authenticated. Use this method to retrieve values from Session() and store to process properties.

The following example demonstrates in a Process class:

```foxpro
FUNCTION OnAuthenticated()

*** Read custom Process Properties
this.cAuthenticatedUserName = Session.GetSessionVar("AuthenticatedUsername")
this.nAuthenticatedUserPk = VAL(Session.GetSessionVar("AuthenticatedUserPk"))

*** You could also load up the user object so it's always available
*this.oUserSecurity.Load(this.nAuthenticatedUserPk)
*this.oAuthenticatedUser = this.oUserSecurity.oUser

ENDFUNC
```

Typically you'll use this method in combination with [wwProcess::OnAuthenticateUser()](VFPS://Topic/_2LH0UZF0J) to implement custom authentication.