Returns either a collection of matching key values from a query string. Use this is if you have multi-select values from a form, or you simply need to pass multiple values for a single key to a URL. 

This method returns a collection of matching values, or an empty collection if no matches are found.

```text
https://somesite.com/page.tst?query1=value1&query2=value%202&query1=value+1+Number+2&query3=value+3
```

To retrieve the `query1` which has two values:

```foxpro
loQuery1 = Request.GetQueryStringMultipleCollection("query1")  

lnCount = loQuery1.Count  && 2
lcQuery1 = loQuery[1]
lcQuery2 = loQuery[2]

pcOutput = ""
FOR EACH lcValue in loQuery FOXOBJECT
    pcOutput = pcOutput + lcValue + "<br>"
ENDFOR

Response.Write("Values:<hr> " + pcOutput)
```