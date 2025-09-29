This is a property that is set with a Eval string expression that is called after all rows of the grid have been rendered. This gives you a chance to potentially write code at the very end of the table before the end tags of the table a rendered. 

A common scenario for this might be to add a footer with totals to the bottom of a table and returning the bottom row(s) as a raw Html string.

This event fires after all rows of the table have rendered and before the end table tags (`</table>`) are rendered. The data source will be at the end of the source but it will still be open and available.

> ####  @icon-warning Important 
> This function **must** return a string.

### How it works
This event is implemented as a property string that is called with FoxPro's `EVALUATE()`. Whatever you put in the string is what's executed as a single operation. Typically you'll pass the name of a UDF (`DoSomething()`) or object instance method (`poBusObject.DoSomething()`).

The following example implements the method called inside of a Web Connection wwProcess class:  

```foxpro
loConfig.OnAfterRowsRendered = "Process.AfterRowsRendered(loConfig,poBusObj)"
```

You can optionally pass parameters, just make sure the parameters are in scope and evaluate properly *at the time the of execution* near the end of the DataGrid processing and match the signature of the function/method you are calling.

Here's how you hook up the event by providing an expression:

```foxpro
*** Inside of a wwProcess Class
PRIVATE poBusObject
poBusObject = CREATEOBJECT("InvoiceBusiness")
...

loConfig.OnAfterRowsRendered = "Process.AfterRowsRendered(loConfig,poBusObj)"
```

Note that the full expression is provided in quotes. If you're calling a function or method make sure you pass the complete syntax with parenthesis and parameters applied.

To get a reference to the `HtmlDataGridConfig` object in the handler method pass in **loConfig** as a parameter. Any other in scope (ie. `PRIVATE` or `PUBLIC` variables) can also be passed as long as they are defined at the right scope.

The handler implementation for the call above looks like this:

```foxpro
FUNCTION AfterRowsRendered(loConfig,loBus)
LOCAL lcHtml

***  Generate a raw Html string
lcHtml = "<tr><td colspan="5"></td><td>" + ;
         loBus.GetReportTotal() +;
         "</td></tr>"

RETURN lcHtml
ENDFUNC
```