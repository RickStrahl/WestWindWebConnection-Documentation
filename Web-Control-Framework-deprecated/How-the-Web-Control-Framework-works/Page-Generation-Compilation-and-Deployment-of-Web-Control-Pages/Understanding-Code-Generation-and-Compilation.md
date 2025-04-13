WebControl Framework Pages require parsing and compilation into FoxPro code. A WCSX style script page is parsed into a Visual FoxPro PRG file that can then run independently of the page on disk. 

Pages can be compiled in two ways:
<ul>
* **Automatically in wwProcess**  
When Web Connection processes WCF Pages and [Server.nPageParseMode](vfps://Topic/_1PM15HBJS) is set to 1 or 2, Web Connection  automatically parses and compiles pages every time you access the page. The WebPageParser class is used to perform compilation. The code is then compiled and executed and then completely unloaded, so that the page can be recompiled again on the next hit. The WebPageParser compiles both the current page and any contained visual WebControls. 
Once an application is ready for deployment nPageParseMode should be set to 3 to simply execute pages and not compile them. When running with nPageParseMode set to 3 the PRG files are not required looking either for PRGs in an EXE or the FXP file on disk.

* **Manually using the WebPageParser.prg file**  
You can also manually compile pages using the WebPageParser class. This f
The WebPageParser class provides the capability to parse a script Page or script Control into a Visual FoxPro class. The parser is easy to use for manual operation if required.
</ul>

<div class="notebox">**Runtime Compilation Note:**  
If you are running projects as compiled EXE\APP files and you have included the Web Control Framework page into the project, nPageParseModes 1 and 2 will have no effect on the running application and not update changes to the pages unless you stop and restart the EXE/APP. Web Connection will re-generate the PRG files, but your application uses the compiled version in the project in this case, so no changes are applied. If you want to run EXE/APP files during development make sure you exclude the Web Control Framework Pages and Controls from the project. Our recommendation is that if you are debugging and making frequent changes run the PRG files rather than APPs or EXEs.
</div>

### Using WebPageParser Class to parse scripts into PRG Files
The WebPageParser class handles parsing of script pages into Visual FoxPro classes. The simplest way to compile a file is the plain PRG file syntax:

```foxpro
* ** Compile a page
DO webpageParser WITH "d:\westwind\wconnect\webcontrols\testpage.wcsx"

* ** Compile a user control
DO webpageParser WITH "d:\westwind\wconnect\webcontrols\testpage.ascx",2
```

This is an interactive format that shows the output generated in a code window immediately after compilation. You can turn off the display by passing a third parameter of .t. for silent operation. The above syntax also support batch compilation if you pass wildcard characters:

```foxpro
DO webpageParser WITH "d:\westwind\wconnect\webcontrols\*.wcsx"
```

### How it works
WebPageParaser looks at the source file specified and automatically reads the GeneratedSourceFile attribute of the wwWebPage tag to write the output to the specified file. If the file does not exist it's created with the generated class, plus a stub class for your code at the top. After the first generation this stub class is never touched again unless you delete the file (Note if you change the Id of the generated class in the script page you will have to adjust the stub code and your class name manually).

<blockquote>**Make sure all Control Classes are loaded!**  
The Parser instantiates all Control classes on a page in order to read member information on these classes. This means that all control classes must be in the SET PROCEDURE/SET CLASSLIB stack along with any dependencies that might get loaded via Initialization code. So make sure that before you run the compiler you run your application or do whatever is necessary to load all PRG and VCX based classes into memory.
</blockquote>

The WebPageParser is a class with a number of options which you can customize and utilize for your own compilation tasks. You can look at the source to see the gory details - it' a pretty hairy piece of code. But there are three core methods you can work with from highest level to lowest level.

<ul>
* **ParseFiles**  
This is the batch compiler that compiles any files in the wildcard list. This is great to point at a directory of files and let 'er rip.

* **ParseToFile**  
Parses an individual file and is called by ParseFiles. You pass a filename as input and the parser picks up the output filename from the script file optionally (unless you explicitly pass an output file). The output goes to file and generates the loader stub and implementation stub on the first generation. Once the file exists only the generated class is replaced leaving the rest alone.

* **ParsePage**  
This is the low level entry point method that takes an input file and creates a single class definition from it. This is the pure generated class, without the loader stub and the implementation class.
</ul>

To get an idea how the parser works here is the code that runs when you run DO WEBPAGEPARSER:

```foxpro
#INCLUDE wconnect.h
DO WCONNECT   && 
CLEAR

oParser = CREATEOBJECT("WebPageParser")
oParser.Parsemode = 1     && 1 Page 2 Control
oParser.CompileOutputFile = .F.
oParser.ParseToFile(lcPhysicalPage) 

MODIFY COMMAND (oParser.GeneratedSourceFile) nowait
? oParser.GeneratedSourceFile
? oParser.cErrorMsg
```

### Runtime Auto-Compilation using Server.nPageParseMode
You can also automatically compile pages using the Server.nPageParseMode property and setting it to 1 (Parse and Run) or 2  (Parse, Compile and Run). When set, Web Connection loads script pages, parses them, compiles them, dynamically loads the script page, executes it and unloads the classes from memory. This process which is not very efficient makes it possible to make changes to script pages without stopping and restarting the Web Connection application. This means you can add new controls, modify properties etc. and see those changes immediately. 

nPageParseModes include: 1 - Parse and Run, 2 - Parse Compile and Run, 3 - Run only. The parse options are primarily meant for development. Option 3 the recommended mechanism for deployed applications due to FoxPro's inability to consistently unload loaded classes from memory across instances.

The PageParsemode is configurable in <yourApplication>.ini in the PageParseMode key and via the Web Connection Status form.

Note that it is possible to compile pages at runtime with compiled applications, both using automatic compilation (Modes 1 and 2) or by using WebPageParser. But although it seems tempting to allow dynamic compilation and it works with a single instance of Web Connection, in a multi-instance scenario only the current instance will be able to see the changes to the compiled code. Therefore if you recompile at runtime make sure you unload and reload all Web Connection servers.

### Compilation of user controls
User Controls are special constructs that act much like a WebPage but are treated like a control that can be dragged onto a form. They allow designing of reusable visual components. As mentioned above User Controls are treated by Web Connection like pure programmatic controls and thus they load and always stay loaded even in DebugMode. They do not unload like Pages. Thus any change to a UserControl requires a start and stop of the Web Connection server.

User Controls can either be compiled individually like Page using WebPageParser (pass 2 as the second parameter), or they can auto-compile as part of a page that references them. So if Page1.wcsx references UserControl1.ascx, Page1 will trigger a compile of UserControl1. 

### Deploying compiled Files
The page compiler creates standalone PRG files that consists of two classes for each page or control: A generated class and an implementation that you use to attach code to for mainline and event processing. The file generated is a standalone PRG and it is **dynamically** invoked  by the Web Connection framework, which means it's not automatically sucked into a Project file. 
This means you have to either:
<ul>
* Deploy the compiled FXP files 
* Explicitly add these PRG files into your FoxPro Project file
</ul>
The latter approach is probably the preferable one, but it requires some diligence in making sure you catch all the generated files. The easiest way to force files into your project including future projects is to add a method to one of your existig already loaded PRG files (or create a new one that gets added). In it add a dummy method that loads any of the PRG files by executing them. This method is actually never called but it will force the VFP project manager to import the files.

```foxpro
* MyApp_Loader.prg
* ** Add any user and custom controls
SET PROCEDURE TO webcontrols\CustomUserControl.prg ADDITIVE
RETURN

* ** Pull in 'evaluated' project files
FUNCTION DUMMY

DO GuestBook_page.prg
DO DataGrid_Page.Prg

ENDFUNC
```

There's also an utility on the Web Connection | Tools Menu for Drag and Drop PRG and VCX references into a PRG file.