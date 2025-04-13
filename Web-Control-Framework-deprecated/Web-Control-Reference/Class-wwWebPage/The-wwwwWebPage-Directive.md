This directive is the most vital directive in the page scripting framework as is the root element and required for the parser and runtime to figure out which class and program to execute.

**Note that <ww:wwWebPage> is required in any parsed script page and must contain the *runat*, *ID* and *GeneratedSourceFile* attributes! All three of these attributes are required!**  

These 3 attributes are special attributes. Otherwise you can use any attributes that map to the properties of the wwWebPage class.

### Where and how to use this tag
The tag must sit at the top of the document and wrap all of your page content. Only content inside the tags will get generated. The only thing that should proceed the <ww:wwWebPage> directive is ASP.NET page directives required for VS.NET to properly render the document in the Web Form editor. These are not required for Web Connection, but for VS.NET/ASP.NET. 

```html
<%@ Page Language="C#" %>
<%@ Register Assembly="WebConnectionWebControls" 
    Namespace="Westwind.WebConnection.WebControls"
    TagPrefix="ww" %>
<ww:wwWebPage runat="server" 
    ID="CustomerList_Page"  
    GeneratedSourceFile="controldemo\CustomerList_page.prg" >
    <html>
    <head>
        <title>Customer List Demo</title>
        <link href="westwind.css" rel="stylesheet" type="text/css" />
    </head>
    <body>
        <form id="form1" runat="server">
            <div>
               <ww:wwWebTextBox runat="server" id="txtName">Text to Display</ww:wwWebTextBox>	<br>		
               <ww:wwWebButton runat="server" id="btnSubmit">Go</ww:wwWebButton>	

... rest of the page here
		
            </div>
        </form>
    </body>
    </html>
</ww:wwWebPage>
```

Note that the <ww:wwWebPage> tag wraps all of the content of the document.

### Required Attributes
The following attributes are required and must be present on the wwWebPage tag.

**ID**  
This will become the name of your class that handles these requests. The first time you 'run' this page a class is created - 2 actually - that execute the page as FoxPro code. The ID specifies the class you write code for. The ID + _WCSX is a generated class that contains the control definitions parsed from this HTML page. We'll see what this looks like in a minute.

**GeneratedSourceFile**  
As you might have guessed this is the source code location where the class is generated. This file contains two classes one with your custom code and the generated code for the page. The page is regenerated everytime you 'run' the page in development mode. Your custom code is kept separate and is not touched by the code updates.

The path specified should be relative to where your FoxPro application is running! So I use *ControlDemo\Customerlist.wcsx* which goes into the ControlDemo directory beneath my Web Connection directory. You can also specify a ~ to specify a path relative to the physical location of the file (ie. the Web Directory). I don't recommend this but the option is there.

**runat="server"**  
All controls require this directive, which the parser uses to find controls. If you don't put this directive on a control the parser won't find it and it's skipped over. Get used to it - you'll need it if you manually edit and create controls in text.