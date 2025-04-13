Returns either a single key's value from a query string, or the entire query string as a whole.

Query strings are used to pass small bits of request information on the URL by using keys following the `?` in the URL. Values are specified by key value pairs like this:
```text
https://somesite.com/page.tst?query1=value1&query2=value%202&query3=value+3
```

You can then retrieve individual values with:

```foxpro
lcQuery1 = Request.QueryString("query1")  && value1

*** Note: query string values are automatically Url decoded (spaces in this case)
lcQuery2 = Request.QueryString("query2")  && value 2
lcQuery2 = Request.QueryString("query3)  && value 3
```

To capture the entire raw Query String don't pass a parameter:

```foxpro
lcQuery = Request.QueryString()  && query1=value1&query2=value%202&query3=value+3
```