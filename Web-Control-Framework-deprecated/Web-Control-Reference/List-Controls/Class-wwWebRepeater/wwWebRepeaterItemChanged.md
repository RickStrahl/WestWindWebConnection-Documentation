Event that fires just after a new item has been loaded. You can hook this event to affect processing that occurs just before the data item is rendered.

You can also use this event to set the datasource for nested data items. For example, it's possible to nest another Repeater inside of the repeater to display child items. When you do the child repeater needs a mechanism to load the child data and this event can serve this task.

If you want similiar behavior but more control over exactly where in the template processing code fires you can also use an embedded expression like this:

```html
ww:wwWebRepeater runat="server" id="repItems" DataSource="tt_cust">
<ItemTemplate>
    Start<ww:wwWebLabel runat="server" Text="hello" ControlSource="tt_cust.company"></ww:wwWebLabel> 
    <br /> ---
    <br />
    <%= this.Page.repItems_Changed()" %>
    <ww:wwWebRepeater runat="server"  id="repChildren" DataSource="timebill">
   <ItemTemplate>      
      <ww:wwWebLabel ID="WwWebLabel1" runat="server" ControlSource="Titems.enterd"></ww:wwWebLabel>
   </ItemTemplate>
   </ww:wwWebRepeater>
   <hr />
</ItemTemplate>
</ww:wwWebRepeater>
```

Note that this method has access to a DataItem object when dealing with an Object Array Data source. The DataItem will reflect the current active object.