Web Control Framework (WCF) Pages parse HTML markup, plus script code and Web Connection Controls into FoxPro code in PRG files. The framework is basically a code generator that turns your Page into a FoxPro class that is executed and which in the end produces HTML (or other) output. Web Connection parses the page, compiles it and runs it automatically taking care that in the development environment classes are unloaded so you can edit them without having to restart the server.

The parsing and compilation process involves:

<ul>
* **Parse the Web Control Framework Page**  
Page parsing is typically performed when accessing the page through a browser. When the page is accessed the Web Control framework finds the matching GeneratedSourceFile and creates or updates this class from the markup in the page. Web Connection performs some caching of the PRG file so it is only regenerated when the WCF Page has been updated (file date of the WCF page is newer than the PRG).
* In order for page parsing to work[ Server.nPageParseMode](vfps://Topic/_1PM15HBJS) has to be set to 1 or 2*.

* **Compile and run the Web Control Framework Page**  
Once the page has been parsed (or has been previously parsed if cached), Web Connection will try to load the page. When running in Server.nPageParserMode 1 and 2 Web Connection will load the PRG file and then load the class, call it's internal Run() method. After running the class is unloaded. When running in pre-compiled mode (Server.nPageParserMode 3) Web Connection simply tries SET PROCEDURE TO the class file, and then load the class. In this mode it assumes either that the class is compiled into the running EXE or a compiled FXP file exists at the GeneratedSourceFile location.
</ul>

You can set Server.nPageParser mode either in code, or by setting the PageParserMode key in YourApp.ini or on the server status form:

![](IMAGES%2Fmisc%2FPageParserMode.png)

As a general rule you will use the following [Server.nPageParseMode](vfps://Topic/_1PM15HBJS) settings:

<ul>
* **Development Mode** 
Run with Server.nPageParserMode = 1 (or possibly 2 if you're running a compiled EXE) which will always try to compile the WCF Pages as soon as you change them.

* **Deployment Mode**  
Run with Server.nPageParserMode = 3 which uses only compiled pages either from within the EXE or in external FXP files that live in the GeneratedSource path specified of the WCF Page.
</ul>

For more info check the following more detailed links:

<%=  ChildTopicsList(oHelp,"TOPIC") %>