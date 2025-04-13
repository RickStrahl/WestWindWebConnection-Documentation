Return `.T.` or `.F.` if a SQL search expression (a `WHERE` clause essentially) can be found. 

> Only the first item found is returned so you probably want to reserve this for known lookups of unique items.

By default the method sets the `.oData` member unless the `llNoDataMember` parameter is set in which case the `.oData` member is left as it was.