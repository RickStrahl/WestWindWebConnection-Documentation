Script and Template pages can also embed Markdown markup. This means you can freely mix HTML and Markdown text in the same script or template.

To embed Markdown simply use the `< markdown >` and `< /markdown >` tags and embed your markdown text in between.

```markdown
<div id="EmbeddedMarkdown">

<markdown>
    ## This is a Markdown header
    This is a block of **Markdown text** embedded inside of the HTML document. 
    It's automatically parsed.
    
    Here's what it does:
    * Parses Markdown text
    * Creates HTML
    * Leaves Script expressions intact
    
    > ### Note
    > Markdown cannot contain script expressions.
    
</markdown>

</div>
```

You can use any markdown text in your Script or template between the `< markdown >` and `< /markdown >` tags.


### Script Expressions in Markdown Tags
You can use `<%= %>` expressions and `<% %>` inside of `<markdown>` blocks:

```html
<markdown>
   Welcome back <%= poModel.Username %>

   ### Your Orders
   <% 
      SELECT TOrders 
      SCAN
   %>
      **<%= TOrders.OrderNo %>** - <%= FormatValue(TOrders.OrderDate,"MMM dd, yyyy") %>
   <% ENDSCAN %>
</markdown>
```

But you should be aware how this feature works.

Markdown is expanded into HTML and the `<% %>` expressions are left in the expanded HTML. This means:

* Processing is done before the script is processed
* Each individual expression is turned into HTML first with the HTML holding the `<% %>` expressions

Understanding this important as it can have some unintended consequences as single line markdown expressions may behave differently than grouped items.

For example:

```html
<markdown>
# Welcome to <%= version() %>

* Item 1
* Item 2

<% for lnX = 1 to 10 %>                
* Item <%= lnX %>
<% endfor %>

More text here.
</markdown>
```

Creates different list items for the static list above, and the generated list below. The lower list will end up creating a complete list for each item iterated (`<ul><li>item 1</li></ul>`) because the Markdown is interpreted prior to script execution. 

IOW, behavior of grouped items like tables and list may not behave quite the way you expect.

Still this feature can be quite useful especially for embedding single script expressions inside of Markdown.