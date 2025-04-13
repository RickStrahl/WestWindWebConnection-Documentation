Fires just before an item row is rendered in the DataGrid. You can optionally return the full content of the row as a string.

The event is fired before any output for the row is generated and if you return a string value that value is assumed to be the full output of the row.

You can also modify the columns of the grid in order to change the behavior. For example, assume for a second that you have a set of values that you want to group with different colors by a date range. Each data gets a different color. You can implement this with the following code. Assume this table has an entered field and the cursor is sorted by date and databound:

```html
<ww:wwWebDataGrid ID="gdLinksByDay" runat="server" CssClass="BlackBorder" 
            width="95%" PageSize="25" AlternatingItemCssClass=""  RowRender="gdLinksByDay_RowRender">
    <Columns>
        <ww:wwWebDataGridColumn ID="colEntered" runat="server"
                 Expression="Entered"
                 HeaderText="Entered" HeaderAttributeString="align='center'" 
                 FieldType="D" ItemAttributeString="style='font-size:8pt'">
        </ww:wwWebDataGridColumn>
        ... More Columns
    </Columns>
</ww:wwWebDataGrid>
```

The key is the RowRender event which points at this method:

```foxpro
DEFINE CLASS MyPage as wwWebPage

* ** Internal Tracking Values for next group
lAlternate = .F.
dLastDate = {^2000/01/01}

FUNCTION OnLoad()
ENDFUNC

FUNCTION gdLinksByDay_RowRender()

* ** Grab the current DataItem from cursor
* ** DataGrid DataSource Cursor will be in scope here
lvValue = TQuery.Entered

* ** Check last date - if changed flip the alternate
* ** switch which tells us to change background color
IF this.dLastDate # lvValue
   this.lAlternate = !this.lAlternate
ENDIF
IF THIS.lAlternate
   lcAttributes = "class='gridalternate'"
ELSE
   lcAttributes = ""
ENDIF

* ** Apply the CSS Class to each column
FOR lnX = 1 TO this.gdLinksByDay.Columns.Count
   loCol = this.gdLinksByDay.Columns.Item(lnX)
   loCol.ItemAttributeString = lcAttributes
ENDFOR

this.dLastDate = lvValue

ENDFUNC
*  ReferingLinks_Page ::  EnteredFieldExpression

ENDDEFINE
```


This code basically checks for a changed date in the cursor and based on that value changes the CSS Class of each of the columns in the grid to display in a common color. So all the dates of the same kind display in the same color.