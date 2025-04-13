Expression you can specify to have fired just before a row gets rendered. The expression MUST return a string.

This handler is useful for:

* Injecting additional HTML content - like another row
* Calculating totals as they are rendered

>#### @icon-exclamation-triangle Important  
>This function must return a string!

To set up the expression you'd use:

```foxpro
loConfig.OnAfterRowRender = "Process.AfterRowRender(loConfig)"
```

Note that the full expression is provided in quotes and that any objects accessed or passed must be in scope. To get a reference to the HtmlDataGridConfig object in the handler method pass in **loConfig** as a parameter.

This event fires while the record of the row just rendered is still active.

A more complete example looks like this:

```foxpro
*** Use the HtmlDataGrid Helper function (easier)
loConfig = CREATEOBJECT("HtmlDataGridConfig")
loConfig.PageSize = 10
loConfig.Width = "800px"

*** Set up a delegate FoxPro expression that is called 
loConfig.OnAfterRowRender = "Process.GridGroupFooter(loConfig)"

*** Now render the grid
lcHtml = HtmlDataGrid("tt_Cust",loConfig)	
Response.Write(lcHtml)
```

You then implement the expression as a handler for this - in this case as a Process class method. The expression must return a string - either an empty string or a string that gets embedded as a result. This method implements group headers based on the company name changing - when it changes a row is injected displaying the name of the company.

```foxpro
PROTECTED FUNCTION GridGroupFooter(loConfig)
LOCAL lcHtml, llNewProject

lcHtml = ""  && Return empty if not matched

llNewProject = .F.

*** Read next record to see if it's a new project
if !eof()
   SKIP
   IF pcLastProject != TProjects.Project
      llNewProject = .T.
   ENDIF
   SKIP -1
ENDIF   

*** Private variable
pnProjectTotal = pnProjectTotal + TProjects.Cost

if !llNewProject
   return ""
ENDIF   


lcHtml = lcHtml + "<tr><td colspan='3'></td><td>" +;
                  TRANS(pnProjectTotal + ;
                  </td></tr>"

RETURN lcHtml
ENDFUNC
```

You can also replace the entire HTML of the current row rendered by setting the `loConfig.RowContent` property to a string that represents the HTML of the row to render. 

```foxpro
PROTECTED FUNCTION AfterRowRender(loConfig)

loConfig.RowContent = "<tr><td colspan='3'></td><td>Overridden!</td></tr>" 

*** Still have to return a string
RETURN ""
```