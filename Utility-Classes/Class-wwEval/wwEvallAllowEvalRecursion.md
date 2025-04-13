Determines if the MergeText function allows recursive expression. So if an expression returns another set of markup expressions (<%= Version() %>) by default the expanded expression is not evaluated.

Note that this behavior also affects operation of wwUtils.MergeText() and Response.ExpandTemplate(), both of which now do not recurse expression syntax. If you want to use those functions and explicitly support recursion you'll have to write a little extra code like this for ExpandTemplate for example:

```foxpro
loEval = CREATE("wwEval")
loEval.lAllowEvalRecursion = .T.
lcResult = loEval.MergeText( FILE2VAR(Request.GetPhysicalPath) )
Response.Write(lcResult)
```