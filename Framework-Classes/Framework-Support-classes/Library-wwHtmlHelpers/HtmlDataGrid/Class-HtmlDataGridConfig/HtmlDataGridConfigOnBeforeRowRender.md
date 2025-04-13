Event called just before a row is rendered in the data grid. The record to render is already loaded at this point.

This event is useful for injecting content before a row is rendered such as group headers.

This handler is useful for:

* Formatting the entire row with custom styles or CSS classes
* Injecting header rows into the grid
* Formatting HtmlDataGridConfig Columns based on certain conditions

>#### @icon-exclamation-triangle Important  
>The expression that runs must return a string! Return empty to do nothing, or return a string that returns HTML - typically a `<tr></tr>` block that renders for things like headers, that render before the row.

### Examples

To set up a function handler use `OnBeforeRowRender` like this:

```foxpro
select * from albums into cursor TQuery


loConfig = CREATEOBJECT("HtmlDataGridConfig")

*... other configuration settings

*** Note you can pass loConfig (always!!!) to your function
loConfig.OnBeforeRowRender = "Process.HighlightAlbumRowByYear(loConfig)"

HtmlDataGrid("TQuery",loConfig)
```

You then implement a method the method you referenced - typically on the Process class, but it can be any FoxPro function or class method as long as it's in scope. 

#### Modify Row Styling
This method creates custom styling on the row using the `RowAttributeString` property:

```foxpro
FUNCTION HighlightAlbumRowByYear(loConfig)

lnYear = TQuery.Year  && you can access the iterating cursor

*** figure out some condition
if (lnYear > YEAR(Date()) - 10)
    loConfig.RowAttributeString = [ class="highlight" ]
ELSE
    loConfig.RowAttributeString = ""
ENDIF

*** optionally return a string to inject HTML before the row
RETURN ""
```

Note that the full expression is provided in quotes and that any objects accessed or passed must be in scope. To get a reference to the `HtmlDataGridConfig` object in the handler method pass in *loConfig* as a parameter (regardless of the name you use!). Note that the active cursor/alias will be active when the row is rendered so you can access the cursor as needed in the handler code.

#### Inject another row - like a group header

```foxpro
*** pcLastCompany - PRIVATE string expected to exist
PROTECTED FUNCTION ShowHoursBeforeRowRender(loConfig)
LOCAL lcHtml 

lcHtml = ""  && Return empty if not matched

*** Check if the company in the data row has changed - if it has write out a header row
IF pcLastCompany # Client
   lcHtml = [<tr class="groupheader"><td colspan="6"><a href="showclient.wwd?id=] + custno + [">Client + [</a></td></tr>] + CRLF
   pcLastCompany = Client
ENDIF

RETURN lcHtml   
ENDFUNC
```