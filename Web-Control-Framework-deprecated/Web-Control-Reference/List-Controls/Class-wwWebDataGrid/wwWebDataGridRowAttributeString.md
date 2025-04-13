A complete attribute string that can be applied to the <TR> tag of the data items.

This method is very useful if you override the RowRender event, to allow custom coloring of rows for example by assigning custom CSS classes or background colors.

```foxpro
FUNCTION gdLinksByDay_RowRender

* ** Grab the current DataItem from cursor
* ** DataGrid DataSource Cursor will be in scope here
lvValue = Entered

* ** Check last date - if changed flip the alternate
* ** switch which tells us to change background color
IF this.dLastDate # lvValue
   this.lAlternate = !this.lAlternate
ENDIF

* ** Create the ItemAttributeString
IF THIS.lAlternate
   lcAttributes = "class='gridalternate'"
ELSE
   lcAttributes = "class=''"
ENDIF

this.gdLinksByDay.RowAttributeString = lcAttributes

this.dLastDate = lvValue

ENDFUNC
```