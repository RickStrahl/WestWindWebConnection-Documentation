The wwWebRepeater control provides a template driven data display mechanism. You basically create a template that is repeated for each item in the datasource assigned to the Repeater. The content of the template(s) can contain HTML or other Web Controls to provide dynamic data to display.

There are two templates that can be used to render the repeating content: ItemTemplate and AlternatingItemTemplate and there are HeaderTemplate and FooterTemplate that can be used to render respective header and footer values.

Template content can contain both static HTML markup or controls. The content of the template is rendered for each iteration of the DataSource control.

Here's an example of a template:

```html
<ww:wwWebRepeater ID="repLatestItems" runat="server">
    <HeaderTemplate>
        <div class="gridheader"> 
        Web Log Entries
        </div>
    </HeaderTemplate>
    <ItemTemplate>
        <b><ww:wwWebHyperLink ID="hypTitle" runat="server" ControlSource="TEntries2.Title" UrlControlSource="'NewEntry.blog?id=' + TRANS(pk)"></ww:wwWebHyperLink></b><br />
        <small><i>
            <ww:wwWebLabel ID="lblEntered" runat='server' ControlSource="TEntries2.Entered" ControlSourceFormat="=TimeToC">
            </ww:wwWebLabel></i></small><br />
			<% if TEntries2.Active %>
				<b>Active</b>
			<% endif %>
        
        <small><%= TextAbstract(StripHtml(TEntries2.body),150) %></small>
        <br />
        <p />
    </ItemTemplate>
    <AlternatingItemTemplate>
	  <div class="gridalternate">
        <b><ww:wwWebHyperLink ID="hypTitle" runat="server" ControlSource="TEntries2.Title" UrlControlSource="'NewEntry.blog?id=' + TRANS(pk)"></ww:wwWebHyperLink></b><br />
        <small><i>
            <ww:wwWebLabel ID="lblEntered" runat='server' ControlSource="TEntries2.Entered" ControlSourceFormat="=TimeToC">
            </ww:wwWebLabel></i></small><br />
         <% if TEntries2.Active %>
            <b>Active</b>
         <% endif %>
        
        <small><%= TextAbstract(StripHtml(TEntries2.body),150) %></small>
        <br />
        <p />
        </div>
    </AlternatingItemTemplate>

</ww:wwWebRepeater>
```

The repeater works with a set of template controls. It supports *HeaderTemplate*, *ItemTemplate*, *AlternatingItemTemplate* and *FooterTemplate*. These templates can contain other literal content or additional Web Connection controls. When the control is rendered Web Connection renders each of the templates, binding each of the templates on every item that is currently active. The headers and footers bind once - at the beginning and end respectively - and the ItemTemplates render for each data item the Repeater is bound to.

Notice that there's static HTML, a HyperLink control, some Web Connection Web Controls and as an evaluated expression (<%= %>). You can even use an [IF conditional expression](vfps://Topic/_1OO1EOQ75). All of them work in a repeater template.

In this example the Repeater is bound to a cursor - TEntries2 and the template is referencing this cursor and its fields. 

```foxpro
SELECT TOP 20 Title,Body,Entered,pk FROM Blog_Entries ;
      WHERE Active INTO CURSOR TEntries2 ;
      ORDER BY Entered DESC

* this.repLatestItems.DataSourceType = 1  && This is the default 
this.repLatestItems.DataSource = "TEntries2"
this.repLatestItems.DataBind()  && Hooks up template binding
```

You can bind directly by using expressions or by using a control and assigning a ControlSource. Web Connection will DataBind each of the controls as they are loaded.

You can also bind to arrays and collections as long as they contain objects for the data members. In that scenario instead of the cursor name you get a DataItem object that you can reference for the currently active item.

Here's a contrived example using a collection of objects:

```foxpro
SELECT * FROM tt_cust INTO CURSOR TQuery
this.oCol = CREATEOBJECT("Collection")

SCAN
   SCATTER NAME oData MEMO
   this.oCol.Add( oData )
ENDSCAN

this.repCustData.DataSource = "this.Page.oCol"
this.repCustData.DataSourceType = 3
this.DataBind()
```

The template might look like this:

```html
<ww:wwWebRepeater ID="repCustData" runat="server">
    <ItemTemplate>
        <%= DataItem.CareOf %> <%= DataItem.Company %>
        <hr />
    </ItemTemplate>
</ww:wwWebRepeater>
```

### Nesting
You can also nest another wwWebRepeater inside of a master repeater. Simply define another wwWebRepeater control in the master template to make this happen which can be useful for master detail displays. One tricky thing about this feature is that you will need to somehow feed the child repeater's data source with updated data.

You can use the ItemChanged event to create code that goes out and requeries a cursor to retrieve detail items that the repeater's DataSource is set to. Alternately you can also embed an <%= %> expression into the page and call a page method:

```html
<%= this.Page.GetChildCursor() %>
```

In the ItemChanged event handler or the GetChildCursor method defined you'd then run a SELECT to feed the datasource. Note you may have to save and reset the current alias so as to not interfere with the master SCAN loop.