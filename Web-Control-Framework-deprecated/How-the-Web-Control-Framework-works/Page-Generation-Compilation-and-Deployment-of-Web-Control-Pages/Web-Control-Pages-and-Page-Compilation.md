As you may have surmised from previous topics, in order to run a Web Control Page the page must be compiled. Without compilation a Web Control Page is just text and it won't do anything.

The wwProcess class can run Web Control Pages in two ways:
<ul>
* **Dynamic Page Creation: Server.nPageParseMode 1 or 2**  
In this mode you must be running the Server object nPageParseMode=1 or 2 (Parse & Run or Parse, Compile & Run  - you can set this on the Status Form's Page Parse Mode Options). In this mode Web Connection will read the file, parse it and run it, then unload the page class, EVERY TIME you run the form. This is not very fast as the page is reparsed each time even if it hasn't changed, but it's great for debugging. 

The DebugMode and PageParseMode properties can be set both interactively and programmatically:
<ul>
* Server.lDebugMode  and Server.nPageParseMode properties anywhere in Web Connection Code
* The DebugMode and PageParseMode keys in <yourApp>.ini 
* Web Connection Server Status Form
</ul>

This mode allows you change pages and have Web Connection immediately regenerate and execute the page without stopping the server. Web Connection behind the scenes manages loading and unloading the page class.

Note that if you have any hanging references on the page Web Connection won't be able to unload the page and will then no longer be able to execute the updated code. It will continue to run the code that was previously loaded. Make sure you don't have hanging references! If you do, you'll have to stop the Web Connection Server, release all objects, then start it back up.

* **Run Only Mode: nPageParseMode 3**  
If the Server's nPageParseMode is set to  3 (Run only), Web Connection just tries to the run the file specified in the GeneratedSourceFile attribute of the form which effectively tries to execute the compiled code - a FXP file or compiled code in an EXE/APP file. If the page has not been generated yet - it will fail. If you make a change to the script code that change will not be reflected because the code is not regenerated. 

The DebugMode flag has no effect in this mode.
</ul>

<div class="notebox">**Very Important Project Note:**  
Because pages are loaded dynamically by Web Connection, you have to make sure you add pages to your compiled project manually. If you don't the VFP project manager will not include your generated classes and your custom code. Either add them manually or add a dummy method that is never called to your project that executes each of the pages.</div>