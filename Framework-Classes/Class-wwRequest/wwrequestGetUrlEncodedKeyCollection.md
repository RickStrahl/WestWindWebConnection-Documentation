Allows enumerating and parsing of any Url Encoded list of values into a collection of `Key` and `Value` property objects. 

Allows retrieving:

* All Form Variables (`cFormVars`)
* All QueryString Variables (`cQueryString`)
* All ServerVariables (`cServerVars`)

> Note that `cFormVars` has a dedicated `GetFormVarCollection()` method to retrieve all values. QueryString and ServerVariables don't have dedicated methods and it's for enumerating these that this method is useful.

```foxpro
loQueryCol = Request.GetUrlEncodedKeyCollection(Request.cQueryString)
FOR EACH loQuery IN loQueryCol FOXOBJECT
    ? loQuery.Key
    ? loQuery.Value
ENDFOR
```