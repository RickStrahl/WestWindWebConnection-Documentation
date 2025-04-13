Creates an HTML link (a href) element.

**Examples:**  
```foxpro
? HtmlLink("NewPage.wwd","New Page",[ class="hoverbutton"],"lnkNewPage") 
* <a href="NewPage.wwd" id="lnkNewPage" class="hoverButton">New Page</a>

? HtmlLink("NewPage.wwd")
* <a href="NewPage.wwd">NewPage.wwd</a>
```