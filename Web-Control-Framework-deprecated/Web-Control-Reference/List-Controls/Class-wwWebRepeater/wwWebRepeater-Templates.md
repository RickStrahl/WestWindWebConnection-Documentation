The repeater works by using templates that are applied at various stages in the rendering of the control. You can use each of these templates and bind content to them to cause output to be displayed.

The ItemTemplate is the key template and the only one that is required. The item template is applied for each databound item with each of the templates being explicitly databound so that embedded controls gain access to the current data item (either a cursor's row or an object in the case of an array or collection object).

The following templates are available:
<ul>
* **HeaderTemplate**  
Template applied at the top of the content. Use plain <%= %> tags to bind page level content into this template.

* **ItemTemplate**  
This is the main template that is repeated for each item that the repeater iterates over. Each item causes the template to be databound so if you embed controls any ControlSource values are applied for the current data item. Alternately you can use <%= %> expressions where you can use the cursorname.field (ie. Customers.LastName) or - in the case of object arrays or collections - the DataItem.Value.

* **AlternatingItemTemplate**  
The AlternatingItemTemplate is rendered for every other item in the list that is databound. Generally the content of this template will be identical to the ItemTemplate with the exception of the CssClass or explicit attribute or style assignment (like background) that causes the template to differentiate the alternating item. This template is not required.

* **FooterTemplate**  
The footer template is rendered at completion of the control rendering and placed on the bottom. As with the HeaderTemplate you can apply page level controls or <%= %> expressions in this template. Footers are generally used for displaying totals or other summaries.
</ul>

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