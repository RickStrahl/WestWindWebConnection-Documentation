The wwWebLogin control provides both a visual login control as well as the ability to authenticate users directly against a FoxPro table using the wwUserSecurity class.

This class can act completely independently and provide the UI as well as performing the actual authentication. When a user logs in the control authenticates against the specified User class and sets LoggedIn and IsAdmin properties. It also writes a couple of Session variables. On subsequent requests the class is not accessed again and the two values are merely retrieved from the Session object and if available are used for the authentication ticket.

For more info see [How the wwWebLogin Control works](vfps://Topic/_1O5148KRC)