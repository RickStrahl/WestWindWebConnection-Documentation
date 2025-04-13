Web Connection includes support for adding raw Markdown files with `.md` extension, into your Web application's folder structure and have those files served as HTML merged into a well-known template.

It's easy to drop a markdown file into your Web project like this:

![](/images/stepbystep/markdownstaticpage.png)

and have it rendered as a standalone HTML page.

For this particular Markdown document I simply picked a Markdown file from an old Weblog post, along with an associated image and dropped that into a folder. 

And voila - we have a valid Web page from the Markdown just like that.

### How it works
To get this to work you need Web Connection 6.25 or later.

* Create a Markdown file with an `.md` extension
* Drop it anywhere into the site

The page should be served as HTML from the Markdown using the stock template.

Internally Web Connection uses a `*.md` scriptmap (now added by default in new projects) and maps it to the `wwScriptmaps.prg` handler. This handler specifically handles MD files, parses the Markdown to HTML and then embeds the content into a fixed, well-known template:

```text
~/Views/Markdown_Template.wc
```

You can customize this template as needed. The default template, simply captures the apps layout page and embeds the markdown inside:

```html
<%
    pcPageTitle = IIF(type("pcTitle") = "C", pcTitle, pcFilename)
%>
<% Layout="~/views/_layoutpage.wcs" %>

<div class="container">
    <%= pcMarkdown %>
</div>


<link rel="stylesheet" href="~/lib/highlightjs//styles/vs2015.css">
<script src="~/lib/highlightjs/highlight.pack.js"></script>
<script>
    function highlightCode() {
        var pres = document.querySelectorAll("pre>code");
        for (var i = 0; i < pres.length; i++) {
            hljs.highlightBlock(pres[i]);
        }
    }
    highlightCode();
</script>
```

Two variables have to exist in this page:

* **pcTitle** - the title of the page which is extracted from the document. Either a YAML header if there is one, or the first `#` tag in the document. Otherwise the file name is used for the title.

* **pcMarkdown** - this is the rendered HTML from the markdown document.