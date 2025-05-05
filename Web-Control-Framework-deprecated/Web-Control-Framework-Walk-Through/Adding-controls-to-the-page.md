Actually we were already creating a new page in the last step. It's called HelloWorld.dp. Let's open that page in the Visual Studio Editor. If you switch to Design view you'll see a blank page.

At the top of the page type - Hello World Demo and press enter. Highlight the text and use the Styles dropdown to set the text Heading 1. Add some more text like a link back to the Demo Home Page, select the text and use the Hyperlink icon on the toolbar create the link. The whole enchilada should now look something like this:

![](/images/WebControls/VsNetInitialPageNoControls.png)

Let's first switch to Html Source View. You should now see something similar to this:

```html
<!-- 
     * Set the name of your class in the ID property
     * Point the GeneratedSourceFile at a PRG file in your FoxPro project directory
     * NOTE: the path is *relative* to your executing directory (ie: CURDIR() + GeneratedSourceFile)
     * Remove this block of comment text
-->
<%@ Page Language="C#" 
         ID="Helloworld_Page" 
         GeneratedSourceFile="* ** PATH\Helloworld_Page.prg" %>
<%@ Register Assembly="WebConnectionWebControls" 
    Namespace="Westwind.WebConnection.WebControls"
    TagPrefix="ww" %>
<html>
<head>
    <title>Hello World</title>
    <link href="westwind.css" rel="stylesheet" type="text/css" />
</head>
<body style="margin-top:0px;">
    <form id="form1" runat="server">
    <h1>Hello World Demo</h1>
    <br />
    <a href="/">Demo Home</a>

     <ww:wwWebErrorDisplay runat="server" id="ErrorDisplay" />

    </form>
</body>
</html>
```

Notice the comment at the top of the page which directs you to change the ID and GeneratedSourceFile path, which is the path to the PRG file that acts as the CodeBehind file for this page. 

Change the @Page tag to:

```html
<%@ Page Language="C#" 
         ID="Helloworld_Page" 
        **GeneratedSourceFile="devDemo\Helloworld_Page.prg" ** 
%>
```

This says use a Helloworld_Page class stored in devdemo\HelloWorld_page.prg as the class that handles this page's logic. When this page is now run for the first time, Web Connection will create the Helloworld_Page.prg file and generate a class from the page.  If you should forget to add the path for the class, the first page request will fail because the * ** Path is an invalid path specifier and so you'll get an error.

The @Page tag is is used by Web Connection as the master container for a page. The @Page tag is required for Visual Studio to render the page. The @Register tag is used by Visual Studio to load the Web Connection controls assembly so that the designer can properly display the Web Connection controls with its properties. Both tags are stripped from the final generated output.

The @Page tag REQUIRES that you set two properties:

**ID**  
The ID tag is used to specify the classname for your generated class.The first time you 'run' this page a class is created - 2 actually - that execute the page as FoxPro code. The ID specifies the class you write code for. The ID + _WCSX is a generated class that contains the control definitions parsed from this HTML page. We'll see what this looks like in a minute.

**GeneratedSourceFile**  
This is the source code location where the class is generated. This file contains two classes one with your custom user code and the generated control code for the page. The page is regenerated everytime you 'run' the page in development mode. Your custom code is kept separate and is not touched by the code updates.

The path specified should be relative to where your FoxPro application is running! So above I use *devDemo\HelloWorld.prg* which goes into the DevDemo directory beneath my Web Connection Install directory. 

You can also specify a ~ to specify a path relative to the virtual root directory - ie. the Web path of your project. This allows you to store your PRG/FXP files in the same path as the WCSX or ScriptMapped pages. I don't recommend this but the option is there. For example to create the page in the same directory as the main page:

```foxpro
GeneratedSourceFile="~\MyPage.prg"
GeneratedSourceFile="~\SubDir\MyPage.prg"
```

When choosing this option keep in mind that the page runtime implications. At runtime the page will execute as an FXP and you will need to explicitly copy the FXP file to the server and unload the server to replace file. For more information see the section on [Understanding Code Generation and Compilation](vfps://Topic/_1MD013CTO) of dynamic pages.

Next: [Adding controls to the page](vfps://Topic/_1LY03VJZC)