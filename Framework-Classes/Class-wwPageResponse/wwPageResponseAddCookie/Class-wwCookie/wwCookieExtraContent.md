Optional - Allows adding explicit content to the end of the Cookie string generated.

This property lets you extend cookie behavior to features that might not be addressed by the properties on this object. It is appended to the end of the cookie string generated and allows you to set any custom settings or new features that might not be covered by the existing properties.

Note this code does the following inside of the `ToString()` generation code:

```foxpro
IF !EMPTY(this.ExtraContent)
   lcCookie = lcCookie + "; " + this.ExtraContent
ENDIF
```