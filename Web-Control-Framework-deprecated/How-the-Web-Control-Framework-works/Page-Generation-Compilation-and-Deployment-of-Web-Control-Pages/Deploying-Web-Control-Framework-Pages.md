Web Control Framework (WCF) pages are parsed into FoxPro code files that Web Connection creates and you can add your own custom Page logic to. They are just FoxPro code - a class that lives in a PRG file and are loaded with SET PROCEDURE TO and then executed. WCF Pages parse into a PRG file, and generate an FXP file once compiled and run. 

When you deploy your application to the server you have two options on how to execute your WCF pages:

<ul>
* **External FXP Files**  
In this approach you deploy the FXP files along with WCF pages in the folder referenced in the GeneratedSourceFile attribute of the Page.

* **Compiled into an EXE**  
In this approach you have to explicitly add the WCF PRG files to a FoxPro project so they compile into the EXE. When running like this, you don't need to distribute your PRG or FXP files to the server. Make sure when you deploy your application with Server.nPageParseMode=3 (Run Only).
</ul>

<div class="notebox">**Script File Deployment:**  
It's important to understand that even though pages compile into the project or an FXP file that are fully self-contained,** the script pages (WCSX or custom script mapped pages) are still required on disk** in order to allow Web Connection to figure out which PRG file to execute for a given page. 
</div>

### Distribute FXP Files
Distributing FXP files is pretty straight forward. Just run your application in development with nPageParseMode set to 1 or 2 which produces the FXP files when the pages are modified and then compiled and run.

We recommend this approach primarily if you prefer to store your PRG/FXP files in the same folder as the Web site using ~/  syntax. For example

```html
<%@ Page Language="C#" 
    ID="Default_page" 
    GeneratedSourceFile=**"~\Default_page.prg" **  
%>
```

Alternately you can also create a separate folder in the Web site that holds all compiled files:

```html
<%@ Page Language="C#" 
    ID="Default_page" 
    GeneratedSourceFile=**"~\Code\Default_page.prg" **  
%>
```

The latter might be desirable to explicitly remove access to the source files (although IIS automatically disallows PRG and FXP files from viewing).

It's also possible to distribute your FXP files below Web Connection application execution directory using relative pathing in GeneratedSourceFile ( "webpages\default_page.prg"). This also works but ends up getting confusing since you'd have to update files in multiple locations. If you use a fixed path below your application, we recommend you compile your PRG files into the resulting EXE.

<div class="notebox">**FXP File Copy Warning**  
While FXP deployment might seem really easy, keep in mind that FXP files get locked in memory once they've been accessed, so it might be difficult to update them unless the site is paused.</div>

### Compiling your WCF Pages into an EXE
When WCF Pages are run they are dynamically accessed by Web Connection - they are referenced as strings, so the FoxPro compiler and project management engine don't pick up these files automatically and add them to the project. This means that if you chose to compile in your WCF PRG pages, you have to explicitly add them to the project.

There are a number of ways that you can get your files into project. The easiest is to simply use the project manager and add the files as needed or drag and drop the files from Explorer directly into the project manager. 

You'll want to copy all the generated PRG files. By convention I like to name any Pages with a *_Page* and controls with a *_Control* postfix so it's easy to find the files that need to be manually managed.

One problem with this approach is that if you create a new project the files will have to be added again. A better way is to add a method to a new or existing program file and in it include the PRG files as needed:

```foxpro
FUNCTION WEBLOG_LOADPAGES

SET PROCEDURE TO "WEBLOG\WEBLOGFOOTER_CONTROL.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\WEBLOGHEADER_CONTROL.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\WEBLOGPOSTS_PAGE.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\WEBLOGRSSPAGE_PAGE.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\WEBLOGSIDEBAR_CONTROL.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\ADMINDEFAULT_PAGE.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\NEWENTRY_PAGE.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\SHOWENTRY_PAGE.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\WEBLOG_ROUTINES.PRG" ADDITIVE
SET PROCEDURE TO "WEBLOG\WEBLOGDEFAULT_PAGE.PRG" ADDITIVE

ENDFUNC
```

This ensures that the files always get included and compiled. To help with generating this list of files there's a helper form in the TOOLS directory and on the Web Connection Menu. 

![](IMAGES%5CWebControls%5CProcedureFileDropper.png)

You can then take the output from this window and paste it into your 'loader' routine.

Once you compile your EXE and you run it to serve requests you no longer need the PRG and FXP files. However the WCF script pages themselves must still exist.