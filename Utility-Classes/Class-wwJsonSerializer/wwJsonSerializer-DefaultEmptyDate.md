Allows you to explicitly set a predefined date value for empty FoxPro dates when serializing, since JSON doesn't have the notion of the *empty date*. This is the date value that the serializer applies to FoxPro empty dates when serializing into JSON. 

The default date uses JavaScript's default zero date which is a date of `{^1970-1-1 :}`.