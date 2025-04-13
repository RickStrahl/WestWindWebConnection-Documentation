Markdown Pages is a new feature in Web Connection 7.0 that lets you simply drop any `.md` file into a Web site and render that file as an HTML page in your site's UI context - using the default layout page.

This is a great feature for quickly creating static HTML content like documentation, a simple blog, documents like about or terms of service pages and so on. Rather than creating HTML pages you can simple create a markdown document and drop it into the site and have it rendered as HTML.

### How to Use
To use this feature you simply can drop a `.md` file into your Web Connection Web folder for your project. You can then navigate to the file via your browser:

http://west-wind.com/wconnect/Markdown/posts/2018/09/25/FixWwdotnetBridgeBlocking.md

to access the page which looks like this:

![](///images/misc/markdownpagerendered.png)

Notice the rich formatting of the topic, as well as the stock site chrome which is derived from the site's Layout page.

This page is a previous Blog post I created for my Web Connection blog and which is created using Markdown in <a href="https://markdownmonster.west-wind.com" target="top">Markdown Monster</a>. The post is a self contained Markdown document with related images stored in the same folder as the Markdown file.

All that needs to happen to render this HTML content in this fashion is to drop a Markdown file into the folder along with any dependent relative resources - images in this case:

![](///images/misc/markdownfileinfolder.png)


### How it works
This feature is implemented as a Web Connection process extension (in `wwScriptmaps.prg`) that intercepts `.md` files, and parses the file for a title. The handler reads the file as is from disk, converts the Markdown into HTML, and then calls `Response.ExpandScript()` to render the fixed `views/Markdown_Template.wcs` template.

You can customize how the Markdown is rendered via a `Markdown_Template.wcs` script page. By default this page simply renders using nothing more than the layout page as a frame with the content rendered inside of it. But the template is customizable. 

Here's what the default template looks like:

```html
<%
    pcPageTitle = IIF(type("pcTitle") = "C", pcTitle, pcFilename)
%>
<% Layout="~/views/_layoutpage.wcs" %>

<div class="container">
    <%= pcMarkdown %>
</div>


<link rel="stylesheet" href="~/lib/highlightjs/styles/vs2015.css">
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

Three values are passed to this template:

* **pcTitle** - the page title (parsed out from the document via YAML header or first # header)
* **pcFileName** - the filename of the underlying `.md` file
* **pcMarkdown** - the rendered HTML from the Markdown text of the file
 

### Html Sanitation On or Off
By default pages rendered through this mechanism are rendered with HtmlSanitation on meaning that any JavaScript code inside of the Markdown won't render.

If you want to enable JavaScript code inside of your Markdown to enabled embedding widgets or activating any custom script interaction, you can do this by setting:

```
Markdownpagesanitized=Off
```

in your application's `yourApp.ini` file or setting the `Server.oConfig.lMarkdownPagesSanitized = .F.` flag.