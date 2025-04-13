Allows quick rendering of an ASP style script page that contains embedded <% %> tags for FoxPro script expressions and code blocks. Runs as a full FoxPro program.

Unlike [MergeText](vfps://Topic/_S8X04MEZW), this function can execute structured code blocks like this:

```html
<% if lnQty > 5 %>
   Qty: <%= lnQty %>
<% else %>
   Minimal Qty: <%= lnQty %>
<% endif %>
```

The script template is compiled into a single FoxPro PRG file that is executed so the entire script page acts like a single self contained function with Function scope.

RenderAspScript is a thin wrapper around the [wwScripting class](vfps://Topic/_1D60VR75H) to provide single line execution of scripts.