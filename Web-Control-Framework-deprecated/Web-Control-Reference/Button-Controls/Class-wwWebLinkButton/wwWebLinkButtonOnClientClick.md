Sections are treated in a special way in Script and Template pages as they are essentially embedded as literal values into the layout page. This is no problem except when the section block gets even moderately large and there's a `Line is too long` error thrown by FoxPro due to FoxPro's literal string limitations.

A section like this:

```html
<% section="headers" %>
<style>
    #Editor {
        width: 100%;
        height: 55vh;
        min-height: 350px;
    }
</style>
<% endsection %>
```

Gets rendered into the following FoxPro code essentially using a string literal:

```foxpro
wwScriptSections.Add("headers",[] + CRLF + ;
 [<style>] +  CRLF +;
 [	 #Editor {] +  CRLF +;
 [		  width: 100%;] +  CRLF +;
 [		  height: 55vh;] +  CRLF +;
 [		  min-height: 350px;] +  CRLF +;
 [	 }] +  CRLF +;
 [</style>])
 ```

The script engine turns the section into a function call that receives a literal string of the section content.  If this seems akward you're right, but there's no good way around this literal text as the text here is generated at compile time and the only way to be available at runtime it has be literally embedde into a literal string so it can be used later.
 
This works just fine as long as the block of text is not very large. But FoxPro has some nasty string size limitations. Specifically a literal string can only be ~8k in size. So any textblock over 8k in size will give you:
 
> Line is too long

which is given by the FoxPro compiler at the time of page compilation. I've been back and forth on this over the years and never found a good solution to this problem. The way to do this is to generate an array of string blocks, but inside of the script parser and generated code this is extremely unwieldy to generate and keep track of the array.

### Workarounds
So what do you do if you have a huge block of CSS or script or a subsection of code?

There are a couple of ways to do this and both aim to basically externalize the content. The external content can then be any size at all.

* **Script and CSS** - Use an external file and link it
* **HTML** - Create a partial

#### Externalize Scripts and CSS
The most common use case for Sections tends to be CSS and page specific Script that needs to be rendered at the Layout page level.

If you have a ton of CSS or Script the simplest solution is to simply create a new `page.css` or `page.js` file and link it into the page. This is generally a better idea than littering the page with a bunch of markup that is easier to manage externally anyway. 

Not only does this fix the sizing issue, but it's also a generally accepted good coding practice.

#### Use Partials for HTML and Code
If you're dealing with a large block of HTML and code block, it's very easy to simply create a partial page.

For example in a recent application I needed to create 3 modal dialogs on a local page. These Modals take a fair bit of space - definitely more than 8k and sure enough I got the Line too long error. 

These modals need to render at the document root under the `<body>` element in order for Bootstrap to work properly. 

So I added a section **into the Layout page**:

```html
<div>
...
</div> <-- other document content above -->

<%= RenderSection("bottom-page") %>

</body>
```

In the content page I then reference the section and instead of the actual Modal dialog markup I reference a Partial to externalize the content:

```html
<% section="bottom-page" %>

<%= RenderPartial("~/WriteMessage_Modals.wwt") %>

<% endsection %>
```

Now my section that is rendered into the page is absolutely tiny and renders like this:

```prg
wwScriptSections.Add("bottom-page",[] + CRLF + ;
 [] +  CRLF +;
 [-%= RenderPartial("~/WriteMessage_Modals.wwt") %-] +  CRLF +;
 [])
```

<small>(the scripting engine fixes up the funky -% %- syntax into proper script tags)</small>

This reduced my massive HTML block into a tiny block. The `WriteMessage_Modals` page now contains all the modal dialog HTML and it gets rendered just as before but with the modals isolated which is awesome.

This is much cleaner and it works well.