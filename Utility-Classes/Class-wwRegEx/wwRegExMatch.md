Matches a regEx expressions and returns the Match collection

```foxpro
loRegEx = GetwwRegExObject()
loMatches = loRegEx.Match(lcData,'".*?((^")|[^\\]")')
IF !ISNULL(loMatches) AND loMatches.Count > 0
   lcValue = loMatches.Item(0).Value
ENDIF
```

If items are in the collection:

* **Item(0)** - the entire match string
* **Item(1-n)** - Sub group matches