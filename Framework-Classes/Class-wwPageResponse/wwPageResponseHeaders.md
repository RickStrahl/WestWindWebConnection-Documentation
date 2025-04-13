A collection of individual HTTP headers that are to be added to the request header. The Headers of the Response can be set at any time during the request as they are cached and written at the end of the request.

The collection contains a pair of name and value pairs that can be accessed like this:
```foxpro
Response.Headers.Add("Expires","-1")
Response.Headers.Add("Custom","CustomValue")

Response.Headers.Clear()  && Remove all headers
```

You can also use the [Response.AppendHeader](vfps://Topic/_1V60XGWHL)() method which has the same functionality.