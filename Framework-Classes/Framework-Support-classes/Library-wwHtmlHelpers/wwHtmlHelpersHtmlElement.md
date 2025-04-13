Creates an HTML Element by its element name.

**Examples:**  
```foxpro
lcHtml = HtmlElement("div","Hello World") && <div>Hello World</div>
lcHtml = HtmlElement("br")   && <br />

lcHtml = HtmlElement("div","Hello World",[ class="errordisplay"],"divHello") 
* <div class="errordisplay" id="divHello" >Hello World</div>
```