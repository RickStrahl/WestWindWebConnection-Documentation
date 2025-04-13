Provides a very simplistic C# like string.Format functionality. 

Note: This method is a static method on the string object, rather than an instance method:

```javascript
var result = String.format("Hello {0}, the time is: {1}",
                           "Rick",new Date().formatDate("mm-dd-yy HH:mm"));
```

No formatting options are supported in the format expressions and only pure string replacement is performed. So formats like {0:mm-yyyy} for a date will not work for example.