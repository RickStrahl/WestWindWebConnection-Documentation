Determines how Web Control Framework Pages are parsed by Web Connection.

1 - Parse and Run
2 - Parse, Compile to FXP and Run
3 - Run only

**1 - Parse and Run**  
Calls WebPageParser on every Web Control Framework page to parse the page. The page is then executed. 

This option should be used only during development. Page rendering is slow, as the operation parses the page, loads the classes, executes it, then unloads all classes explicitly. This is required in order to replace the compiled FXP file.

Previously parsed pages are cached based on the file date of the script file. Only if the script file is newer than the PRG file, the WCF Page is re-parsed into a PRG.

This option only works effectively inside of the FoxPro IDE.

**2 - Parse, Compile and Run**  
Same as Parse and Run except that the WebPageParser tries to explicitly compile the page after parsing. It is possible to use this option at runtime in an EXE since it attempts to replace the existing FXP file. 

Note that this option is slower than the Parse and Run because it also needs to compile. 

<div class="notebox">**Multi-Instance Warning**  
Web Connection tries to unload the FXPs to minimize (but not eliminate) locking conflicts. Compilation is not guaranteed to work if multiple instances are running as FoxPro locks active FXP files that have classes loaded. 

FXP files might be locked and then cannot be updated. Another issue is that although you can update the FXP file for one instance, other instances will not recognize the new FXP file and instead run any cached versions already loaded. In order to make sure that compiled files are visible in all instances either run with a single instance or reload instances after making changes. </div>

**3 - Run only**  
This is the preferred mechanism for deployed applications. This option causes Web Connection to simply execute the FXP file or project embedded compiled file without any special checks.

This option is considerably faster than the previous options as no parsing or compilation takes place and highly recommended for deployed applications

### Deployment options
If you use Run Only you get fast and reliable operation because you are simply executing pre-compiled code, but with it you cannot change the page unless you recompile either an EXE file (for project embedded page PRG files) or the individual FXP files and re-deploy them on the server.

The Parse, Deploy and Run option can be an option to dynamically recompile pages on site if you temporarily switch into single instance operation (from the ISAPI Admin page). You can execute the new pages and thereby dynamically recompile them.

Another alternative is to locally compile all page classes and then put the application on Hold (ISAPI Status Page), copy the FXP files and then take the Hold off. 

I personally prefer embedding pages into a Project and update the entire EXE file on the server. It is simply more efficient to manage and test a single EXE file as opposed to keeping track of a large number of PRG/FXP files to upload to a server.