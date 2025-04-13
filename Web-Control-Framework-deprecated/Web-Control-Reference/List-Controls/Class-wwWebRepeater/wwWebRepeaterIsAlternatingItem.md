Flag that determines if during rendering an alternating item is active. 

Allows you to create quick switches in the ItemTemplate without having to have a separate AlternatingItemTemplate.

```html
<ItemTemplate>
<div class="<%= iif(THIS.Repeater1.IsAlternatingItem,'gridalternate','gridnormal') %>">
   <%= company %><br>
   <%= Lastname %> - <%= DateEntered %>
</div>
</ItemTemplate>
```