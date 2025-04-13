The WebRepeater class provides a template driven repeatable display bound to a datasource. The repeater supports Cursors, Object Arrays and Object Collections for databinding.

Repeaters work by using templates for displaying content. It has an ItemTemplate, Alternating Item template footer and header templates which can in turn contain other controls, expressions and literal text.

```html
<ww:wwWebRepeater runat="server" id="repItems" 
               ItemChanged="repItems_ItemChanged" DataSource="tt_cust">
<HeaderTemplate>
   <h1>Customer List</h1>
</HeaderTemplate>
<ItemTemplate>
   Company:  <ww:wwWebLabel runat="server" Text="hello" ControlSource="tt_cust.company"></ww:wwWebLabel> 
   Name:  <%= tt_cust.Careof %>
   <hr>
</ItemTemplate>
<FooterTemplate>
   <small>generated at: <%= DateTime() %></small>
</FooterTemplate>
</ww:wwWebRepeater>
```

**Note:**  
In order for the *ControlSource binding* to work you **have to** call DataBind() on the repeater either explicitly or through the Page's DataBind() method. <%= Expression %> binding always works regardless of whether DataBind() was called or not.