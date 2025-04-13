### Version 7.12
<small>not released yet</small>

* **[Console Update Project Resources Helper](VFPS://Topic/_0R10SLEAN#updateprojectresources)**  
Added a new `UPDATEPROJECTRESOURCES` Console Command that can be used to update an existing project with the resources from an updated Web Connection installation. This updates the Web Connection Modules, the Web Connection Web Server, Web Connection dependent Dlls (wwDotnetBridge, wwipstuff, json), the stock libraries, the Web Connection support scripts. Also added this to the main Console Form as UI operation that prompts for project path and operations to perform. This should make much easier to update existing project with the latest bits from a Web Connection.

* *

* **Refactoring the Administration Page**  
Rebuilding the Administration page to use the single `ModuleAdministration.wc` page to provide all administration tasks rather than having `Admin.aspx` and the Module administration page. This will help consolidate changes in one place and also use a single authentication mechanism. Updated what's displayed on this page as well and re-grouping items into subgroups as well as adding some additional system values like .NET version, running environment and a few options that can now be changed right on the page via toggles or input fields.

* **[New wwServer::ProcessHitBinary()](VFPS://Topic/_5QY1A8UPD)**  
Due to problems with encoding in ASP.NET Core related to Unicode string conversions there's now a new `ProcessHitBinary()` method on the server that receives a BLOB as input for request data, and returns BLOB data for output. This leaves all the string decoding and encoding predictably to the FoxPro end and lets the COM server simply process the raw binary data directly. This also removes an extra conversion and should remove some additional memory usage that previously was required for the Unicode string conversion. Both the ASP.NET Handler and .NET Core Middleware now call this method.

* **[wwUtils IsDotnetCore() Helper](VFPS://Topic/wwUtils%3A%3AIsDotnetCore)**  
Support function that checks if .NET Core is installed. Returns the highest version number.

* **[wwWebServer::IsWebServerInstalled()](VFPS://Topic/_5PU0OQFKN)**  
Helper function that checks to see if the given Web Server type is installed and ready for configuration. Use the standard Web Server keys ("IIS","IISEXPRESS","WEBCONNECTIONWEBSERVER", "APACHE") to check if a given Web Server is installed. Internally used by the Console.

### Version 7.10
<small>March 10th, 2020 &bull; [Release Notes](https://www.west-wind.com/wconnect/weblog/ShowEntry.blog?id=958)</small>

* **[Preview: Local .NET Core Development Server](VFPS://Topic/_5LW0YSXQ9)**  
We've added a new local, self-contained, .NET Core based Web server that can be used instead of IIS to develop your Web Connection applications locally and also in production (on IIS). This new server uses .NET Core 3.x and can generically run the Web Server from the command line. Advantages are: Pre-configured for Web Connection, all configuration in one place, non-Admin, runs from the command line, can run any Web Connection application out of a folder. It's now also possible to run the Web Connection Web Server (in File Mode) **on Linux or Docker** with Web Connection FoxPro Server running on a separate Windows machine.

* **Support for Non-Admin Console Operations**  
If you're creating new non-IIS using IIS Express projects or the new Web Connection Web Server, you can now do so without running as Administrator assuming you're installing into a folder where you have permissions to write. You still get an Admin request warning, but you now can continue to install.

* **Update Module/Middleware Administration Page**  
Number of cleanups on the Module/Middleware Administration pages for Web Connection with easier updates to items and a cleaner view of which mode (COM or File) is active. Also additional system information and updates are provided to make it easier to see relevant data all in one place.

* **[Add wwCookie Class for Response.AddCookie() improvements](VFPS://Topic/_5M50NOCS1)**  
Added a new `wwCookie` class to make it cleaner to add cookies with all of the available HTTP Cookie features that are available. Rather than passing a ton of parameters to `Response.AddCookie()` you now can pass an object with simple properties set.

* **Windows Authentication for Web Connection Server Requests**  
Previously Web Connection server requests using the built-in Windows based authentication used only **Basic Authentication**. We've now enabled **Windows Authentication** so you can use either Windows or Basic or both. This also means you can now run with **only** Windows Authentication enabled in your Web Site as both the ASP.NET requests and Web Connection requests can authenticate with it.

* **Simplify Live Reload Configuration**  
Added new WebConnectionModule that handles `.htm` and `.html` LiveReload script injection when LiveReload is enabled. This removes the need to explicitly enable an extra handler for these extensions. The module automatically detects whether live reload is enabled and only injects content into HTML files when enabled. This simplifies configuration to the single `LiveReloadEnabled` flag in `web.config`. The WebConnectionModule is automatically configured for new projects.

* **Add Live Reload Retry**  
When sitting on a page and shutting down the Web Server the Web Socket disconnects and after that the page previously would no longer refresh on changes. This update continues pinging for a Web Socket every few seconds after failure to try and reconnect to the server. This allows for server updates/shutdowns and reconnecting after the server comes back.

* **Fix: Live Reload Socket for Https Requests**  
Fix issue where Live Reload was failing in the browser when running on an HTTPS page. Page would work but browser would console log an error in the WebSocket Live Reload script. Fixed and you can now use https.

* **[New wwDotnetBridge.InvokeTaskMethodAsync() to call Async .NET Methods](VFPS://Topic/wwDotNetBridge.InvokeTaskMethodAsync)**  
Added a new method to make it easier to call `async` .NET methods that return `Task` or `Task<T>` results. This method takes a callback handler object reference that is called on completion or failure of the method call. Similar to `InvokeMethodAsync()` which calls non-async methods asynchronously but natively intercepts the `Task` result and fires events on completion.

* **`SftpFile::IsDirectory` Property**  
Added a new `IsDirectory` property to the SftpFile object returned in the `sftp::FtpListFiles()` method to simplify figuring out whether an entry is a file or directory.

* **Log Server User Account**  
We now log the server user account in Web Connection FoxPro server requests in the request log. This can be useful to ensure you can see which account your FoxPro server is running under.

* **Fix: Perf Counter Permissions**  
Trap errors in Web Connection Module Server list related to PerfCounter access - if access is not allowed, perf-counters are turned off. 

#### Breaking Changes in v7.10

* **LiveReload Configuration Change for `web.config`**  
If using LiveReload you will need to change the configuration to use the new `WebConnectionModule`. Using the new module removes the need to add/remove the old handler when switching modes - it now works off the `LiveReloadEnabled` flag internally so you only have to set one setting.
  
    **Remove old Handler**: 
    ```xml
    <handlers>
       <add name=".LiveReload_StaticHtml_wconnect-module" path="*.htm*" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" />
       ... leave additional handlers
    </handlers>
    ```

    **Add new Handler**:  
    
    ```xml
    <modules>
      <add name="WebConnectionModule" type="Westwind.WebConnection.WebConnectionModule,WebConnectionModule"/>
    </modules>
    ```

* **wwRequestLog has Structure Changes**  
Please make sure you delete the request log (`wwRequestLog.dbf` by default) or add an `account v(15)` field to the table. Latest version detects if the field is missing and automatically deletes the table and creates a new one otherwise.


### Version 7.09
<small>October 22nd, 2019</small>

* **[Preview: Local .NET Core Web Server Support](VFPS://Topic/_5LW0YSXQ9)**  
We've added a self contained .NET Core Web server that can run standalone without a Web Server installed, or as a backend service inside of IIS. The server can be run from the command line and has a number of options, including automatic support for Live Reloading of content. You do need to have [.NET Core 3.0 installed](https://dotnet.microsoft.com/download/dotnet-core/3.0/runtime). This is currently in preview and there's no explicit configuration support and all info on how to use this new server both for development and behind IIS is described in the documentation topic.  

* **[Preview: .NET Core 3.0 Support with `wwDotnetCoreBridge`](VFPS://Topic/_5LW0Z0590)**  
wwDotnetBridge allows interop with .NET and now there's basic support for .NET Core. r `wwDotnetCoreBridge` allows loading and executing of .NET Core components. As with the full framework version, wwDotnetCoreBridge hosts its own copy of the .NET Core runtime and then proxies into it create types and values that make it possible to access most .NET Core features from Visual FoxPro.

* **Add Edit Button to Web Connection Status Form**  
Added an edit button that makes it easy to open the site in an editor. There's a new `CodeEditorCommandLine` that can be used to launch an editor in the Web Site or the full project so you can quickly jump to editing the site. The default editor assumes VS Code (`code ..\`).
  
* **Fix: Error Message Code Display**  
Fixed error message code display to be properly HTML encoded. If the code contained HTML to generate the error display would render the HTML. Fixed with proper HTML encoding so the code displays properly as code.

* **Fix: wwDotnetBridge Guid Results and Properties**  
Changed behavior of wwDotnetBridge methods that return Guid values or accss Guid Properties. Guids are value types and fixed up by wwDotnetBridge, but previously used the obsolete `ComGuid` type. This has been changed to use the standard `ComValue` type and the `GetGuid()` method. So a method that returns a Guid returns a `ComValue`, as does a Guid property accessed with `GetProperty()`.

### Breaking Changes in 7.09

* **ComGuid Class Retired for Guid Handling**  
Any wwDotnetBridge code that previously access Guid values, now uses the [ComValue](VFPS://Topic/Class%20ComValue) helper, which includes `GetGuid()`, `SetGuid()` and `NewGuid()` helper methods. This is a breaking change, but provides better compatibility with all other special handling types which also use the `ComValue` class.

### Version 7.08
<small>September 3rd, 2019</small>

* **[Simplified Live Reload Configuration](VFPS://Topic/_5HM1E6KMV)**  
Refactored the way that Live Reload is hooked up to a 2 step process: Set the `EnableLiveReload` switch and - for optional HTML file processing - enable the LiveReload Html Handler that intercepts requests to any `.html` and `.htm` files. This simplifies the configuration process which previously required adding a custom module and setting `runManagedModulesForAllRequests` which had performance implications. See docs for more info on the new configuration features.

* **Visual Studio 2019 Addin Async Loading Enabled**  
Updated the Visual Studio addin to support async loading so the addin no longer shows load warnings on startup.

* **Fix JSON Handling for Large Strings and JSON Buffers**  
JSON size is limited by the FoxPro 16 meg limit, but due to the inefficiencies of JSON generation presizing a buffer the JSON string parser uses an overly pessimistic pre-sized buffer to hold JSON text that is passed to internal APIs. Changed the logic to maximize the buffer size at FoxPro's Max string size and handle errors if the buffer is overrun more gracefully. This will allow JSON string parsing up to the FoxPro 16 meg limit now where as before it was of the potential max buffer size.

* **Fix AppPool Creation Bug in New Installations**  
Fix bug where application pools in IIS weren't properly attached to the newly created sample site on first creation.

* **Cleanup Startup Launch.prg Handling**  
After setup and new project Launching now clearly shows what it's doing with messages to the desktop, so you new users can effectively re-run the application without having to look in the docs. `DO LAUNCH` always shows options now.

### Version 7.06
<small>*May 25th, 2019*  
[Release Notes Blog Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=952&id=952)  &bull; [Breaking Changes](#breaking-changes-in-7.06)
</small>

* **[Server Live Reload for Server Code Changes](VFPS://Topic/_5HM1E6KMV)**  
7.0 introduces updated Live Reload functionality without using Browser Sync. Rather there's now native support for live reloading in the Web Connection .NET Module. Live reload detects changes in Web client pages as well as server source code files. On the client changes are detected and immediately refresh the browser when saved. On the server changes are detected causing the Web Connection server to be shut down and restarted plus refreshing the browser.
<small>**Note:** This feature requires Windows 10 or Server 2012R2+ to work </small>

* **[Consolidated Launch.prg Behavior](VFPS://Topic/_5H60Q6VU5)**  
Combined various Launch and Environment setup options into a single launcher that can launch your Web Connection applications in a variety of ways. Modes include: **IIS**, **IISEXPRESS**,**SERVER**,**NONE**. You can simply launch any of the modes with `Launch("IISEXPRESS")` or simply `DO LAUNCH` for the defaults. 

* **[wwUtils FixComErrorMessage()](VFPS://Topic/_5GM0TMZD8)**  
Simple helper function that strips the Ole Dispatch error prefix from the front of a COM error message leaving just the actual error message text.

  > You can create a new `Launch.prg` for existing projects by generating a new project,copying `Launch.prg` into your project and changing the configuration variables at the top of the file (usually the project name and virtual). The rest of the file is generic and portable.

#### Breaking Changes in 7.06

* **Changed the `Server.oConfig.lLiveReloadServer` to `lLiveReloadEnabled`**  
Due to the changes in Live Reload functionality and for consistency the `lLiveReloadServer` flag naming has changed. This flag was introduced in `7.05` and generated in the `MyAppMain.prg` main program file of new projects.

* **Make sure to update WebConnectionModule.dll and Microsoft.WebSockets.dll**  
The new Live Reload functionality is implemented inside of the WebConnection .NET Module in `webconnectionmodule.dll` so to use this new feature you need to update that file in all your Web projects. Also a new DLL, `Microsoft.WebSockets.dll` has been introduced to enable the Live Reload functionality. 

### Version 7.05
<small>*May 7th, 2019*</small>  
<small>[Release Blog Post](https://west-wind.com/wconnect/Weblog/ShowEntry.blog?id=951) &bull; **No Breaking Changes** &bull; [Update Notes](https://webconnection.west-wind.com/docs/_2jw0bpb4d.htm)</small>



* **wwHttp now supports String Downloads > 16mb**  
You can now download data larger than 16mb into strings. FoxPro has a 16mb string limit but with careful usage you can access strings much larger than 16mb as long as the strings are not updated. wwHttp has always supported downloading larger files directly to file and this is just another option that works by default.

* **wwHttp large File Download Speed Improvement**  
Changed the wwHttp buffer sizing behavior to dynamically size the buffer depending on the download size. Larger buffers drastically improve download performance of large HTTP payloads easily improving 5 fold for megabyte sized downloads.

* **Add Option to not Allow Html in Markdown Parser and Function**  
The `Markdown()` function and `MarkdownParser` class now have options to disallow HTML markup inside of any Markdown parsed. This can be useful to cut down on possible XSS attack vectors that go beyond the XSS prevention already built into the Markdown parsing process.

* **wwSFTP Client now fails if renci.ssh.dll is missing**  
wwSFTP now immediately fails if the SSH library used for the SFTP connection cannot be found on startup. Previously this resulted `Undefined COM Error` on sending - you now get a specific warning that points at the missing assembly.

* **Updated Weblog Sample Comment Management**  
Comments are now moderated with an email sent when a new comment arrives, and authenticated users can remove comments in a post or in the main comment list. This fixes a serious SPAM targeting issue with un-moderated spam. 

* **Performance Improvements For Request Processing**  
Optimized a number of API calls commonly used by reusing API declarations for a number of common operations. Removing repeated `DECLARE` calls on API calls for many often reused functions should trim a little overhead in request processing times inside of the Fox server.

* **Fix: DLL Naming Problems in New Project Wizard**  
Fixed bug with New Project Wizard that failed to copy multi-segment named DLLs into the new project folder correctly. Specifically, `newtonsoft.json.dll` and `renci.sshnet.dll` were truncated to (`newtonsoft.dll` and `renci.dll`). Fixed. Please update your project folders with updated DLLs from this release.

#### No Breaking Changes in 7.05

* There are no breaking changes in 7.05 but as always you'll want to upgdate your DLLs in your projects to the latest from the Web Connection folders. Update the Deploy folder DLLs and the `\web\bin` folder dlls.

### Version 7.04
<small>@icon-clock-o March 6th, 2019</small>

<small>**No Breaking Changes** &bull; [Update Notes](https://webconnection.west-wind.com/docs/_2jw0bpb4d.htm)</small>

* **wwApi ZipFolder() and UnzipFolder()**  
.NET based folder zip functions that work of source and target folders on disk. Basic zipping and unzipping features only but handles special files and long file names better than the old `ZipFiles()` and `UnzipFiles()` functions.

* **Add support for Binary Data to wwXml::XmlToObject/ObjectToXml**  
wwXML can now serialize and deserialize binary data (of type `BLOB` or `Q`) to and from XML using base64 serialization.

* **[wwSql.nTimeout Property](VFPS://Topic/wwsql%3A%3AnTimeout)**  
Allows you to set the SQL Connection timeout globally before a connection is made.

* **wwProcess::nAuthenticationTimeoutSeconds**  
Added a property to control the authentication timeout for the session cookie set for authentication.

* **GetFileName() and GetDirectoryName() wwUtils Helpers**  
These methods are similar to `JUSTFNAME()` and `JUSTPATH()` except they preserve the case of the input path.

* **Refactored Server Error Handling**  
The wwServer class has some updates that simplify how errors are handled, displayed, logged and emailed with dedicated methods that handle these tasks. New methods in include `wwServer::SendAdminEmail()` and `wwServer::ErrorMsg()` that is used to display an error page.

* **New Project Wizard now copies DLLs to Deploy Folder**  
When you create a new project, the Wizard now automatically copies all Web Connection dependent DLLs to the Deploy folder, so that running standalone without the IDE and the generated `config.fpw` doesn't cause errors due to missing DLL dependencies. It also makes it clearer that those DLLs have to present for deployed applications.

* **Fixes to the `Launch.prg` Startup Program**  
Fixed a number of small issues related to how `Launch.prg` is generated and started initially when a new project is generated.

### Version 7.02
<small>@icon-clock-o January 24th, 2019</small>

<small>[Release Blog Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=948) &bull; [Breaking Changes](#v702breakingchanges) &bull; [Update Notes](https://webconnection.west-wind.com/docs/_2jw0bpb4d.htm)</small>


* **[New wwDynamic Class](VFPS://Topic/_5CK0PF06K)**  
Added a new dynamic object class that allows for creating or extending objects with dynamic properties that are created as you reference them. Ideal in scenarios where you need to return 'tuple' like object results from methods or where you dynamically need to build up object structures in user code.

* **Add Launch.prg to Project Template and Samples**  
New projects now have a `Launch.prg` generated that makes it super easy to start up a Web Connection application from FoxPro. The file supports launching either IIS or IISExpress (if not running it will launch it) and navigate to a default page that you can change. It'll set up default paths, bring up the Web connection server, open the start page for the project. The PRG can be customized for your specific environment and it's a great way to have an easy way to start running your application for the first time.

* **Setup and New Project Wizard Improvements**  
Both the Setup and New Project Wizard now show detailed information about how to launch your applications in the FoxPro desktop. Instructions show how to launch your server (via the new `Launch.prg`) and what URL to navigate to. Paths are automatically configured. Setup automatically launches the sample application now, the New Project Wizard displays a message on the desktop.

* **Setup now Prompts for IIS Installation**  
The Setup wizard now explicitly asks to make sure that you have IIS or IIS Express installed and if not takes you to the documentation with installation instructions. This hopefully prevents people from installing Web Connection when the Web Server is not installed yet.

* **WebLog Example converted to MVC Style Scripts and Templates**  
The <a href="https://west-wind.com/wconnect/weblog" target="top">Sample Weblog Application</a> that ships with Web Connection has been re-written to work as an MVC style application from the original Web Control Framework application. Most of the UI remains the same, but the underlying framework has been switched to use MVC style Models and Scripts to present that Web UI.  There have also been a few enhancements including support for Markdown comments, a new Comment editor, and an integrated mechanism to delete comments and posts through the main interface rather than in a separate admin screen. This application is shipped in the Web Connection `\Samples\Weblog` folder.

* **[wwHttp::AddPostKey()](VFPS://Topic/wwHTTP%3A%3AAddPostkey) tcExtraHeaders Parameter**  
Added a `tcExtraHeaders` parameter to `AddPostKey()` that allows specify extra HTTP headers for multi-part form values posted.

* **wwHttp .Get(), .Post(), .Put(), .Delete() Methods**  
Added wrapper methods for specific Http Verb operations for clarity. `HttpGet()` was always a misnomer for non `GET` operations and these functions are meant purely for code clarity. They have the same parameters as the `HttpGet()` method.

* **wwHttp.GetCertificates()**  
You can now get a list of TLS certificates installed on the local machine to potentially pass to requests.

* **[wwHttp::lDecodeUtf8](VFPS://Topic/wwHTTP%3A%3AlDecodeUtf8) to automatically decode UTF-8 Content**  
Added a new wwHttp::lDecodeUtf8 property that is `.t.` by default to decode UTF-8 content based on the `Content-Type` header.

* **<a href="Class wwCollection" target="top">wwCollection Enhancements</a>**   
Added `Find()` method to `wwCollection` to allow for finding elements by their value. You can now search any type of element including objects. New `Set()` property allows setting a value if it exists or add a new one if it doesn't. A new `RequiresUniqueItems` property allows collections that can't have duplicate values. The `Item()` method fixes a few scenarios where non-matched items would fail and now return null.

* **wwUtils [GetRelativePath()](VFPS://Topic/wwUtils%3A%3AGetRelativePath) Function**  
Added a new function that returns a relative path. Unlike `SYS(2014)` this function returns the path in proper case. 

* **wwUtils [SaveFileDialog()](VFPS://Topic/wwUtils%3A%3ASaveFileDialog) and [OpenFileDialog()](VFPS://Topic/wwUtils%3A%3AOpenFileDialog) Functions**  
Save and Open file dialogs that unlike `PUTFILE()` and `GETFILE()` return the filename and path in proper case so files can be saved and retrieved by their actual on disk filenames.

* **[wwUtils JsonBool()](VFPS://Topic/_5E50P8RWV) Function to embed JavaScript Boolean Values**   
Rounds out routines to write out Json values as string in addition to `JsonString()` and `JsonDate()`. 

* **All Web Connection DLLs and EXEs are now digitally signed**  
Web Connection is now built with a proper build process that builds the entire product in an automated fashion. Previously some parts were using this automated process but it has now been fully automated. As part of that process all West Wind DLLs and EXEs and Setup files are signed with a digital signature to allow verifying authenticity. This will also make it less 'scary' to run the installer for the first time if Windows SmartScreen is is enabled.

<div id="v702breakingchanges" class="updatenotice" style="margin-left: 0;margin-right">

#### Breaking Changes in 7.02

* **wwResponseFile.prg and wwResponseString.prg Consolidated into wwResponse.prg**  
Consolidated the legacy response object classes into the single `wwResponse` class file which now contains all three of these classes. This removes two additional files from a base install. These objects are now only used as pass through objects proxying for `wwPageResponse` that's used for all actual Request object operations.   
**To update:** You'll get compilation errors for missing files - just choose remove when prompted to remove them from the project.

</div>

### Version 7.0
<small>*November 16th, 2018*</small>

<small>[Release Blog Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=944) &bull; [Breaking Changes](#v700breakingchanges)</small>

* **Template Updates to BootStrap 4 and Upated Support Libraries**  
Overhauled the default project templates to use Bootstrap 4 and FontAwesome 5 and updated overall styling to be more up to date. Updated related components like the date `HtmlDateTextBox()` to use **tempusdominus-bootstrap-4** date picker control. Helpers support both Bootstrap 3 and 4, via a compiler switch `BOOSTRAP_VERSION 4`. There have been many tweaks and updates to the styles for cleaner rendering and a number of new support styles have been added.


* **Fix: Remove hardcoded Content Page Paths from Scripts**  
Changed the generated PRG output for content pages so that the content page path is no longer hardcoded into the page code. This fixes issues where if a compiled FXP was moved to a new location it would fail due to an invalid template path. Updated code provides the content page path up the call stack and persists it into the layout page. 


* **[Updated Visual Studio Templates and Intellisense Snippets](VFPS://Topic/_5BB19P27H)**  
We've created 20 or so Intellisense snippets (Ctrl-k-x -> My Html Snippets -> Web Connection) that let you quickly inject common Web Connection and Bootstrap controls/components into the page with minimal effort. We also provide **Visual Studio Code** Page Snippet templates for Layout, Content and Standalone pages.

* **Visual Studio Code Web Connection Code Snippets**   
We've added a number of helpful code snippets for Visual Studio Code for inserting blocks of code for common Web Connection components in scripts and templates are available. Type `wc-` in the editor to see the VS Code snippets.

* **[Generic Markdown Page Rendering](VFPS://Topic/_5AY1D91UT)**  
New projects now include the ability to render any Markdown page with an `.md` extension and render it into HTML. You can simply drop a Markdown file into a folder add any dependent images and have that page rendered. A custom `Markdown_Template.wcs` template can be customized to provide custom page layout for site chrome to match your site's identity.

* **`<% %>` Scripting inside of `<markdown></markdown>`**  
You can also use script and expression tags **inside** of Markdown blocks to effectively script Markdown text.

* **[Updated Markdown Island Support in Scripts and Templates](VFPS://Topic/_4I50RWXWX)**  
Web Connection has support `<markdown></markdown>` tag islands into which you can put Markdown text that is rendered as HTML for a while. Previously you **had to** left align the Markdown content. With this release Markdown content no longer has to be left aligned, rather the parser detects the common space before all lines and strips it.

* **New Projects automatically create local Git Repository**  
If Git is installed locally, the New Project wizard now automatically creates a Git repository in the new project and creates a first commit. You can easily continue committing and always roll back to the initial install. If you don't like the git repo - remove the `.git` folder and it's gone.

* **Authentication Improvements**  
All authentication releated Process properties - `lIsAuthenticated`, `cAuthenticatedUser` and `cAuthenticatedUserName` - now explicitly call `OnCheckForAuthentication()` if it wasn't previously called, ensuring that authentication is always checked when accessing these properties. Previously you had to explicitly call `Authenticate()` or `OnCheckForAuthentication()` which is no longer necessary. A new internal flag ensures that authentication is checked only once on repeated accesses as it accesses the session store to check or retrieve the auth token.

* **wwUserSecurity Improvements**  
Updated wwUserSecurity logic to automatically create `.oUserSecurity` and [.oUser](VFPS://Topic/_26F12NDJO)` objects on each requests. When accessing these objects Web Connection checks to see if they already exist and if not they are created. If a user is not logged in, an empty oUser record is created and you can check for an empty user with [oUserSecurity.IsEmpty()](VFPS://Topic/_5B8198NSV).

* **wwUserSecurity Password Encryption Improvements**  
wwUserSecurity will now automatically encrypt a password that's not already encrypted if `cPasswordEncryptionKey` is set to encrypt passwords. You can now add a non-encrypted password into the file, and next time the user is authenticated the password will be encrypted and written into the database in encrypted form.

* **wwUtils.SanitizeHtml() to sanitize HTML strings**
Added a new SanitizeHtml() function to wwUtils to strip scriptable code out of HTML text. Useful for cleaning up user captured HTML or Markdown text to prevent XSS attacks from user input. Requires .NET.


* **Response.ExpandScriptAsString() and Response.ExpandTemplateAsString()**  
These two functions can now be called to parse a script or template and return the output result as a string rather than writing output into the Response stream as complete pages. Render pages or partials into HTML and use them from process class code directly. Although we recommend using scripts and templates along with Partials to do embed external templates, it can sometimes be useful to 'call' a partial component directly from code. For example a Login Partial component might be rendered into non-script pages and can just be embedded as a string. These functions are mere wrappers around `ExpandScript()` and `ExpandTemplate()` using the `tlReturnAsString` parameter - they are provided for easier syntax and better discoverability.

* **Update Project Template and Add HelloScript Request**  
Add a new script page to the default template that demonstrates running a request using MVC style development by using the a script page with code behind. The script page renders the current request list, displays a message alert box and describes several aspects of the MVC process.

* **[Add BrowserSync.prg to new Project Template](VFPS://Topic/_5CC1A6ADS)**  
New projects now get a `BrowserSync.prg` that can be used for live reloading when you make changes to HTML, CSS, JS and Script/Templates in your project. Requires NodeJs/NPM install for <a href="https://browsersync.io/" target="top">Browser-Sync</a>, but this incredibly useful feature makes it much quicker to iterate server side script, HTML,CSS,JavaScript code similar to the way client side tools work.

* **[HtmlRssFeed()](VFPS://Topic/_5BT19ATQY)**  
Added a method that can produce HTML output from an RSS Feed. The method is customizable with a `HtmlRssFeedConfig` that allows control over number of items retrieved and a number of formatting options for the feed.

* **New File Upload Samples**  
Removed the overly complex plUpload component from Web Connection and instead provided simpler pure AJAX examples as well as a simpler and much more modern uploader called <a href="https://uppy.io/" target="top">Uppy</a>. There are <a href="https://west-wind.com/wconnect/wcscripts/fileupload.wwd" target="top">simple file upload samples</a>, and an example of <a href="https://west-wind.com/wconnect/wcscripts/UppyAndCarousel.wwd" target="top"> uploads displaying in an image carousel</a>.

* **[wwPdfPrinterDriver PDF Printer for wwPDF](VFPS://Topic/_5AS0URRCN)**  
Added a generic PDF driver that can be used with any printer driver that outputs to PDF including the built-in **Microsoft Print to PDF** driver in Windows 10 and Windows Server 2016 and later. This driver handles report printing, capturing and waiting for the output file from the print spooler and dealing with unattended mode issues when running in COM modes.

* **wwDotnetBridge - Automatically unblock DLL**  
Added logic to automatically unblock `wwDotnetBridge.dll`. This should help with many common loader error issues.

* **Improved Error Message for wwDotnetBridge Load Errors**   
wwDotnetBridge has a few rules required in order to run it - specifically related to Internet downloaded-loaded DLLs and loading from a network. Updated the loader error message with common causes and link to documentation.

* **wwDotnetBridge Fixup for List<T> return values**  
Added additional support for `List<T>` return values to `InvokeMethod()` and `GetProperty()` results, which returns these values as a ComArray now.

* **[wwDotnetBridge Support for Creating Generic Types](VFPS://Topic/_5C51DGO92)**  
You can now create generic .NET types like `List<T>` or `Dictionary<t,t1>`. A new method on `ComValue::SetValueFromCreateGenericInstance()` allows you to create a generic type on the `ComValue` structure. Since generic types are not directly accessible from FoxPro (no support for COM interfaces on generic types) interfacing with these types always goes through ComValue and indirect ac

* **[wwProcess::OnLogout()](VFPS://Topic/_58P0M1LNE)**  
Added method hook that is called after a user is logged out. This is useful for removing additional application specific state that might be saved in session or other application data stores.

* **[wwProcess::InitSession(): New llSecure parameter](VFPS://Topic/_S8413MYP2)**  
You can now specify that the Session cookie created can only be used under HTTPS via the `llSecure` parameter. This translates to the `Secure` flag of the cookie created.

* **[wwUtils GetFullPath() Function](VFPS://Topic/_5CD14MRRU)**  
Function that resolves a relative path to a full path but also matches the proper case of the file on disk if it exists.

<div id="v700breakingchanges" class="updatenotice" style="margin-left: 0;margin-right">

### Breaking changes in 7.0

* **Bootstrap 4 and FontAwesome 5**  
Web Connection switches default templates to Bootstrap 4 and FontAwesome 5 (free). Bootstrap and Fontawesome syntax have changed somewhat for these new frameworks. These changes don't affect any existing applications, except for: the `HtmlDateTextBox()` and `HtmlErrorDislay()` which are affected by the changes. If you need to use the old version of Bootstrap use the `BOOTSTRAP_VERSION=3` in `wconnect_override.h`. If you decide to upgrade applications to Bootstrap 4, most bootstrap concepts work, but <a href="https://getbootstrap.com/docs/4.0/migration/" target="top">there are number of differences</a> you have to account for. The list is long but in Web Connection's own templates only very few things were affected mainly panels (cards now) and wells (also cards).

* **wwSql and wwXml Classes now are PRG classes**  
We've switched wwSql and wwXml to run as PRG classes in order to simplify distribution and compilation. You will need to update your project references to the PRG files and any declarations from `SET CLASSLIB TO` to `SET PROCEDURE TO`. You should just be able to do **Replace in Files** in your Favorite editor or explicitly use [GoFish](https://github.com/mattslay/GoFish) to find and fix up any references to **wwSql** or **wwXml**.

* **wwBusinessObject replaces wwBusiness as a PRG class**  
We've replaced `wwBusiness.vcx` with a PRG based version called `wwBusinessObject.prg`. Unlike the wwSql and wwXml classes, we've explicitly renamed the PRG and class to `wwBusinessObject`. The old version will still be available in the `OldClasses` folder for those that need visual classes but any new development will be made only on `wwBusinessObject`. To replace search for any references to `SET CLASSLIB TO wwBusiness` and replace with `SET PROCEDURE TO wwBusinessObject`. Find any class references to `AS wwBusiness` and replace with `AS wwBusinessObject`. If you are using a VCX based subclasses of `wwBusiness` you have to continue using old wwBusiness.vcx **unless you explicitly migrate the visual class to a code class** - you can do that easily with the VFP Class Browser export feature. I suspect the same is true for most applications.

</div>


### Version 6.21 
<small>@icon-clock-o June 15th, 2018  
[Release Blog Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=940) &bull; 
[Download](https://webconnection.west-wind.com/download.aspx)
</small>

* **[wwDotnetBridge support for Event Handling](VFPS://Topic/_57Q019PW0)**  
wwDotnetBridge now supports subscribing to and handling of events via the new `loBridge.SubscribeToEvents()` method. You provide a source .NET object that fires events and a FoxPro object that implements the corresponding event methods that you want to implement. Thanks to Edward Brey who built out this feature in the OSS version of wwDotnetBridge!

* **New .NET Runtime Loader and better loader Error Handling**  
Updated the wwDotnetBridge .NET CLR to using current loader APIs which provide for better error trapping. Load failures should now provide improved error information to client if the .NET Runtime bootstrapping fails.

* **[Console Command for Disable Loopback Checking](VFPS://Topic/_4GI0QL5JB)**   
On servers and now also on newer versions of Windows 10 IIS enforces local loopback check policy which **doesn't allow for local Windows authentication to work**. If you try to access the Admin pages with authentication it will fail if the policy is applied. We've added a new command `console.exe disableLoopbackChecking` to disable this policy.

### Version 6.20
<small>@icon-clock-o April 17th, 2018</small>

* **Support >16meg uploads with wwHttp**  
You can now upload files larger than 16mb with wwhttp by using the new `lUseLargePostBuffer` flag and setting it to `.T.`. Internally this class uses  a file stream buffer to hold the POST buffer.


* **[Updated wwFileStream Class to create >16mb Strings](VFPS://Topic/_56G0MO1OA)**  
The wwFileStream class provides a file based stream that allows creation of large strings that exceed 16mb and can be loaded into FoxPro strings as a whole. FoxPro supports >16mb strings in limited fashion (basically >16mb string can't be mutated), and this class allows you to create those strings.

* **wwProcess::SendAdminEmail()**  
Added helper method to send emails using the configuration settings made in `<yourApp>.ini`. This makes is easier to send confirmation and notification emails from within your code.

* **`message` Parameter for Login**  
The `Authenticate("Login")` method and the stock Login form now support displaying of a message as part of the login form by providing a Query string parameter for example: `Login.wwd?message=Password verified.`.

* **wwJsonSerializer::Serialize() llFormat Option**  
The `Serialize()` method now has an `llFormat` parameter that allows specifying that the content should be pretty formatted. This is in addition to the `FormattedOutput` flag on the serializer itself.

* **Fix: File2Var() UTF-8 Detection**  
Fixed bug in File2Var() that would cause problems with UTF-8 BOM detection in non-Western alphabets.

* **Fix: wwEncryption with Upper ASCII Characters**  
Fixed issue with wwEncryption not properly two way decrypting upper ASCII characters as the Code Page mapping was off. Switched to UTF-8 character sets when encoding and decoding which works on all ANSI codepages as long as the same code page is used to encode and decode.

* **Fix: .NET Module AdminAccount with multiple Accounts**  
Fix issue where AdminAccount was not properly working when multiple accounts were configured - account wouldn't validate against the list. Fixed.

### Version 6.19
<small>@icon-clock-o February 7th, 2018  
[Security Weblog Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=936)
</small>

* **Security Update: Update Admin.aspx Page to Disallow Unauthenticated Remote Access**  
Added logic to the Admin page to disallow access to the Admin page when not authenticated. Local machine access only displays an error message just like previous versions, but remote access now shows an error message and does not display the admin page links.

* **wwRequest::Form support for > 16meg Strings**  
Added support for reading Form variables when the Form buffer is greater than 16megs. FoxPro supports strings > 16megs as long as the string is not manipulated with any string mutating functions. But most importantly you can write a > 16meg string easily into file. [More info.](https://support.west-wind.com/Thread53Q0KU1HZ.wwt). Thanks to Keith Hackett for providing this solution.

* **[wwServer::OnInitCompleted()](VFPS://Topic/_5490MN6XQ)**  
Added an additional wwServer hook method that is fired after the server is initialized. This hook can be subclassed in the wwServer subclass, and is called after the server has been initialized but before the first request and `OnLoad()` are fired. 

### Version 6.18
<small>@icon-clock-o January 9th, 2018  
[Release Blog Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=935)
</small>

* **Add back Apache Support and support Apache 2.4.x**   
Due to a number of requests from customers as well as generous support by way of contract work sponsored by James Heuer, we've added back support for Apache. Apache was dropped in Web Connection 6.15 and is now back with Apache 2.4 support plus a few additional new features.

* **[Improved and simplified Apache Configuration](VFPS://Topic/_1EM0YFUTX)**  
The New Project Wizard now works more reliably with automatically setting up an Apache server configuration. Configuration also has been simplified a bit with new features added to Apache for configuration of virtuals and scriptmaps. There's also better compatibility with direct support for `PHYSICAL_PATH` and `APPL_PHYSICAL_PATH` settings available the Apache request pay load which makes Apache more compatible with IIS's ISAPI implementation.

* **JsonDate() function in wwUtils**  
Added a `JsonDate()` function that creates a JSON formatted date string that is properly UTC adjusted. Function lives in wwUtils but requires wwDotnetBridge/.NET to work.

* **Unload and Reload Individual COM Servers**  
There's a new `shutdowninstance.wc?pid=1234` maintenance request for COM servers that shuts down a running COM instance that will be reloaded on the next hit. This can be used to remotely or internally restart individual instances that may be running with too much memory. Uses the Process ID as identifier.

* **<a href="https://west-wind.com/wconnect/weblog/showentry.blog?id=934" target="top">Fix: Close FoxPro when Web Connection is Running</a>**  
Fix issue where FoxPro cannot be shut down when Web Connection is running inside of the IDE in file mode with *Cannot Quit Visual FoxPro* error message. Hooked window events so shutdown works as expected in file mode. There's now also a [wwServer::Shutdown](VFPS://Topic/_53O00ZTB6) method that is called when the server is shutdown - in file mode only.

* **Fix: wwJsonSerializer UTC Formatting**  
Fixed problem with the wwJsonSerializer where UTC dates weren't properly showing time zones other then current time zone.

* **Fix: wwJsonSerializer Deserialization Property Names**  
FoxPro supports only alpha-numeric plus `_` for property names and this filter ensures that deserialized properties are cleaned up to remove all but those characters via a new `PropertyNameCharacterFilter` property.

<div class="updatenotice" style="margin-left: 0;margin-right">

#### @icon-warning Breaking Changes for v6.18
<a name="v615breakingchanges"></a>

* **wwJsonSerializer::Serialize() now relies on .NET**  
The wwJsonSerializer now uses .NET functions to serialize date values with proper UTC conversions that previously weren't handled. `wwJsonSerializer::DeserializeJson()` already depended on .NET so not a big change.

</div>

### Version 6.17
<small>@icon-clock-o October 16th, 2017  
<a href="https://west-wind.com/wconnect/weblog/showentry.blog?id=933" target="top">Release Blog Post</a>
</small>

* **Better Error information for wwDotnetBridge Load Errors**  
wwDotnetBridge will now report slightly better error information on load failures, when failing to load the runtime or initial wwdotnetbridge instance.

* **Browse Button on the Server Form**  
The server form now has a **Browse Site** button that can open a browser window with the configured **HomeSite** url.

* **JsonSerialize() and JsonDeserialize() Helper Function**  
Added helper functions that let you use the JSON serializer without having to create a new object instance of wwJsonSerializer for each serialization process operation. The helpers simply wrap the `Serialize()` and `DeserializeJson()` methods for easier usage.

* **Updated Process Templates**  
The default wwProcess templates for new process classes have been updated with a few enhancements: The template automatically adds Intellisense references for `THIS` and the Web Connection top level intrinsic objects (Request, Resonse, Server, Session)  via `#if .F.` blocks hide the the directives from executing code but allow the editor to find the Intellisense references. Process classes now also default to Scripts for missing controller/process class methods (ie headless scripts). 

* **Buid Script Updates**   
The `build.ps1` file generated into a new project folder to create a packaged application ready for deployment has been updated to create a more complete deployment package. Previously the script only pulled the EXE and all the Web Connection system dlls required to deploy the binary folder. The updated script adds the Web and Data folders by default and creates the same structure as the project with Deploy/Web/Data top level folders. 

* **Improvements in COM Server Load Times**   
COM Servers now load slightly faster as the COM load sequence has been optimized further after the last releases parallelization of server loading onto pool threads. Servers are now loaded onto MTA threads (rather than STA), which provides faster startup and less overhead in ASP.NET. 

### Version 6.15
<small>@icon-clock-o July 17th, 2017  
<a href="http://west-wind.com/wconnect/weblog/showentry.blog?id=931" target="top">Release Blog Post</a> &bull; [Breaking Changes](#v615breakingchanges)</small>


* **COM Server Loading Changes**  
We've made some changes to the way servers are loaded by separating the `OnInit()` and `OnLoad()` steps to before and after actual server constructor operation. Previously both operations occurred in the confines of the FoxPro `Init()` constructor function which meant any error forced an immediate abort without error information. Now `OnLoad()` is fired **after** the server instance has been created and we can trap errors and display them when the first hit comes in. If an error occurred during load all subsequent requests display that load error - the server is effectively disabled, but you get full error information where previously the server would have not loaded with a cryptic COM error. This should also help with server load speed as load behavior is delayed until after the instance has been returned.

* **Server Count can now be Interactively set on Admin Page**  
You can now set the server count interactively on the admin page when using the Web Connection module. This means you can immediately change the number of instances that start up in COM or File Mode with Auto Server start.

* **Exe Servers hotswapped now automatically re-register for COM**  
When servers are uploaded and hotswapped via the Web Connection module, servers are now automatically re-registered with the `-regserver` flag after successful upload. This should avoid problems if properties or methods were added to the COM server.

* **New Install-IIS-Features Powershell Script**  
Web Connection now ships with a Powershell script called `Install-IIS-Features.ps1` that can be used to install IIS on a Desktop or Server that doesn't have IIS installed. The commands install the Web Service feature/role with all the options required to run Web Connection.


<div class="updatenotice" style="margin-left: 0;margin-right">
<a name="v615breakingchanges"></a>

#### @icon-warning Breaking Changes for v6.15
<a name="v615breakingchanges"></a>

* **Web Control Framework no longer loaded by Default**  
We've disabled the `WWC_LOAD_WEBCONTROLS` flag in `wconnect.h` which means by default the Web Control framework libraries will not be loaded any longer by `wconnect.prg`. To enable Web Control framework loading you can reenable the `WWC_LOAD_WEBCONTROLS` setting in `WCONNECT_OVERRIDE.h`:

```foxpro
#UNDEFINE WWC_LOAD_WEBCONTROLS
#DEFINE WWC_LOAD_WEBCONTROLS        .T.
```
* **Re-Register COM Servers**  
This release makes changes to the COM server interface of the wwServer class which requires for the type libraries to be updated. Make sure you re-register your server with `yourServer.exe -regserver` or by using the new hotswapping features which auto-register the server after upload.
</div>

### Version 6.12 

<small>@icon-clock-o May 30th, 2017</small>  

* **[wwDotnetBridge InitializeDotnetVersion()](VFPS://Topic/wwDotNetBridge%3A%3AInitializeDotnetVersion)**  
An explicit function to initialize the .NET Runtime for the entire application. Although this function doesn't really do anything but create an instance of wwDotnetBridge with a specific version, the function makes it clear that this initializes the .NET version for the entire application.

* **wwUserSecurity::SetProperty/GetProperty**   
Added properties to set dynamic properties on the user record. This allows you to add custom values to the user record without having to extend the schema explicitly (although you can do that as well - add a field and loSecurity.oUser.NewField).

* **Updated GetUniqueId() to be more Random**  
Changed the algorithm of GetUniqueId() using Guids that are redistributed over printable characters. The result is truly random Ids that are more unique with much smaller character counts - as little as 8 characters can provide safe ids for low impact applications with a max of 16 for near 100% fidelity.

* **Add content type to wwHttp::AddPostKey() when uploading files**  
You can now specify an optional content type when uploading files to a server using `.nHttpPostMode=2`. The content type is appended to the file data sent to the server.

* **Fix: HMACSHA Hashing in wwEncryption**   
Fixed a salt encoding bug in the `ComputHash()` function when calling the HMAC specific methods that require a salt value. Previously the salt value were added to the original string and then encoded. Now the salt value is only applied to the HMAC algorithm processor.

* **Fix: wwSFTP Large File Upload**  
Fixed an issue due to a buffer size issue with the underlying SSH library used in wwSFTP. Fixed by ignoring the `nBufferSize` parameter.

### Version 6.10
<small>@icon-clock-o February 2nd, 2017  
<a href="http://west-wind.com/wconnect/weblog/showentry.blog?id=925" target="top">Release Blog Post</a> &bull; [Breaking Changes](#v610breakingchanges)</small>

* **< %: expr % > for Templates and Scripts**  
Scripts and templates can now use automatic HTML encoding by using `< %: expr % >` instead of `< %= EncodeHtml(expr) % >` (spacing added for rendering). This makes it much easier to create XSS safe string output without more verbose syntax. 

* **<a href="https://marketplace.visualstudio.com/items?itemname=rickstrahl.westwindwebconnectionvisualstudioadd-in" target="top">Visual Studio 2017 Addin Support and Visual Studio Gallery</a>**   
The Web Connection Visual Studio Addin now supports Visual Studio 2017 and is also published to the Visual Studio Extension Gallery. This means you can install and update the addin directly from Visual Studio and once installed, the extension can automatically update itself.


* **[New wwSFTP Class for SSH based FTP](VFPS://Topic/Class%20wwSFTP)**  
There's a new wwSFTP class that provides secure FTP (SFTP only, not FTPS) to connect to SSH based SFTP servers. The class inherits from wwFTP so can be interchangeably with wwFtp based FTP code.

* **[wwUserSecurity Password Encryption via cPasswordEncryptionKey](VFPS://Topic/_4U605502P)**  
You can now encrypt your UserSercurity table's passwords by setting the `cPasswordEncryptionKey` when you create your wwUserSecurity instance. When set passwords are automatically hashed before getting saved to the database.

* **[wwEncryption::ComputeHash()](VFPS://Topic/wwEncryption%3A%3AComputeHash) adds HMAC Hash Algorithms**  
wwEncryption::ComputeHash() adds HMACSHA1, HMACSHA256, HMACSHA384 and HMACSHA512 algorithms. HMAC algorithms use complex salting cycles to add complexity and delay to generated hashes using an industry standard and repeatable algorithm.

* **[wwRequest::GetLogicalPath() can return Proxied Urls](VFPS://Topic/_S850QHU25)**  
GetLogicalPath() now gets a parameter that when true will return the original URL specified before a proxy redirected the URL to your application. Useful when using extensionless URLs redirected via Rewrite rules in IIS.

* **Switch to Markdig Markdown Parser**   
Switch markdown parser to use the <a href="https://github.com/lunet-io/markdig" target="top">MarkDig .NET Markdown parser</a> which natively supports Github flavored markdown and many Markdown extensions, including support for auto-links, extra emphasis (strikeout, sub/superscript etc.)

* **wwUtils::GetUniqueId()**  
Allows generation of unique IDs based off a full or partial GUID value. Allows specification of a size that determines the size of the generated string between 15 and 32 characters where 32 is a full GUID.

* **wwUtils::SplitString()**  
String utility like ALINES() to return a collection of strings that allows you to specify a length to break lines at. Uses ALINES[] to capture lines and splits lines longer than specified via MEMLINES(). Useful for any code parsing that needs to ensure string literals don't exceed 255 characters in length.

* **wwDotnetBridge now supports TLS 1.2**   
wwDotnetBridge now sets the default HTTPS protocols settings to allow for SSL3, TLS1, TLS11 and TLS12. The default previously only allowed for SSL3 and TLS1. Overrides `ServicePointManager.SecurityProtocol` on initial load of the .NET runtime. This affects any .NET API called that uses HTTP including Web service calls.

* **wwScripting::CompileAspScript**  
Explicit function that can be used to compile a single ASP Script page using the same mechanism used to run the page. 

* **WCSCompile for pre-compiling Scripts on the Server**   Fixed the pre-compilation link on the Admin page that allows recompilation of Scripts (wcs style FoxPro script pages) on the the entire site. This feature now works recursively and can reliably compile scripts assuming all referenced dependencies used in scripts are loaded by the application before compilation occurs.

* **wwJsonServiceClient::CreatewwHttp()**  
You can now create a new instance of wwHttp to be used in the wwJsonSerializer implementation when making service calls. You can create a new instance of wwHttp that is pre-configured with Authentication, headers etc. and pass it to this method before a service call.

<div class="updatenotice" style="margin-left: 0;margin-right">
<a name="v610breakingchanges"></a>

#### @icon-warning Breaking Changes for v6.10
<a name="v610breakingchanges"></a>

* **wwUserSecurity Table Changes**  
The structure of the Usersecurity table has changed and the table structure has to be updated in order to work. Various fields have been expanded for supporting better security and encryption and field types have been changed to var chars instead of fixed width fields for simpler code and more efficient storage. Make sure you check the structure of the table in [wwUserSecurity::CreateTable()](VFPS://Topic/_1P30TA7OQ).

* **Add Markdig.dll to your Distribution**  
The new Markdig Markdown parser requires a new DLL to be used. If you're using hte `MarkdownParser` class or the `Markdown()` function, make sure to update to this DLL. You can remove `CommonMark.dll`.
</div>

### Version 6.07
<small>@icon-clock-o September 16th, 2016</small>

* **[Angular 2.0 Project Template](VFPS://Topic/_4Q40NERUW)**  
Added a pre-configured Angular 2.0 template that is post-installed into a newly created project. A new walk through describes how to set up a new Angular 2.0 project.

* **wwUtils GetWords() Function**  
Added a new function that parses a string of multi-word text into a collection of words.

* **Updated Project Templates**  
Updated the project templates and Console Project Wizard to generate more comments and notifications when a new project is created, so it's easier to known what to do after the wizard finishes.

### Version 6.05
<small>June 10th, 2016  
no breaking changes</small>

* **View Fox Code from Visual Studio Addin for Scripts/Templates**  
You can now see the **FoxPro Code** option on the Visual Studio context menu for scripts and templates and even static HTML pages if the page contains `SourceFile="<filename>"`. The file name can contain a `~` virtual path like: `SourceFile="~/../Deploy/MyProcess.prg"` which points back to the source code folder from the Web folder.

* **Web Connection Handler File Upload**  
In addition to being able to upload a server EXE file, the handler now supports uploading of arbitrary files to the application's `deploy\temp` folder from the Handler admin page. This makes it easier to push files to the server without having to install an FTP server. Make sure you set the application's request limits to allow larger uploads.


* **[wwDotnetBridge::InvokeMethodAsync()](VFPS://Topic/wwDotNetBridge%3A%3AInvokeMethodAsync) and [wwDotnetBridge::InvokeStaticMethodAysnc()](VFPS://Topic/wwDotNetBridge%3A%3AInvokeStaticMethodAsync)**  
New method that allows invoking .NET methods on an instance asynchronously. You pass in callback object that receives `OnCompleted()` and `OnError()` callbacks to get notified of completion of the async operation.

* **[wwUtils::HumanizedDate() Function](VFPS://Topic/wwUtils%3A%3AHumanizedDate)**  
Function that returns a human readable string date or time (English only) such as *just now*, *10 minutes ago*, *3 hours ago*, *yesterday*. Anything over a year is turned into a short date: *May 1, 2015*.

* **[wwUtils::FormatValue](VFPS://Topic/wwUtils%3A%3AFormatValue) and [wwUtils::FormatString](VFPS://Topic/wwUtils%3A%3AFormatString)**  
Two methods that use .NET string formatting to allow you to use sophisticated string formatting in FoxPro. Easily create nicely formatted date strings, MIME or ISO date formats, non-padded number strings formatted to decimal places with automatic localized formatting.

* **Add `cursor_legacy:cursorName` to wwJsonSerialize table serialization**  
You can now serialize cursors in the 'old' default format which included `Rows` and `Count` properties for the actual data array and record count. Provided for backwards compatibility.

* **Fix wwMaint Server File Editor**  
Fix bug to properly write the correct file out to disk.

### Version 6.0
<small>@icon-clock-o April 2nd, 2016   
<a href="http://west-wind.com/wconnect/weblog/showentry.blog?id=919" target="top">Release Blog Post</a> &bull; [Breaking Changes](#v6breakingchanges)</small>

* **[New Project Wizard](VFPS://Topic/_S8G12B92Q)**  
Created an updated New Project Wizard that creates a self contained directory structure that holds code, data and Web content. The Wizard also auto configures all paths and related links in such a way that the application should be xcopy deployable if you take the entire folder structure to the server. The new structure links back to Web Connection source files in the original Web Connection install folder, and separates logic for your own projects away from the Web Connection core files.

* **[New Project Wizard can now generate a REST Service](VFPS://Topic/_S8G12B92Q)**  
The new project and new process Wizards now include an option to select the process class type to create. You can either create a standard Web Connection wwProcess class or a wwRestProject. The Rest project creates a service method sample.

* **[New Projects generate a Server Configuration Script](VFPS://Topic/_4GR0WBOA4)**  
The New Project Wizard now also generates a `<YourApp>_ServerSetup.prg` file that is FoxPro PRG based script that can configure your Web application. The script is linked from `<YourApp>Main.prg` and can be run it `<YourApp>.exe config` from the command line in a deployed application.

* **[Partial Pages Script and Templates](vfps://Topic/_4DS19CQWR)**  
There is now support for partial page references that allow you to render child pages from your Scripts and Templates. You can simply reference child pages with Web relative paths using `<  %= RenderPartial("~/PartialPage") %  >` where the ~/ points to the root of the Web site. You can also hard reference a file with a full path.

* **Scripts and Templates now support multi-line expressions**  
Script and template pages now can use `<  %= expr() %  >` tags that span multiple lines with line breaks. This especially simplifies use cases where you use complex Html Helpers that include style information. All line breaks are converted to spaces. NOTE: This can cause a problem if you have **literal** strings that include CHR(13) or CHR(10) values.

* **[Native Markdown Support in Scripts and Templates](VFPS://Topic/_4I50RWXWX)**  
Script and template pages can embed `< markdown >` and `< \markdown >` tags to embed Markdown text into the markup. The Markdown is parsed inline as the Script or Template page is parsed.

* **[Improved handling of Extensionless Urls](VFPS://Topic/_3K812UDQ3)**  
Extensionless URLs are common on the Web and this version of Web Connection adds improved support for them by providing a clean re-write rule (needs to be uncommented) and some additional logic in `wwProcess::UrlRewriteHandler` and  `wwProcess::OnUrlRewrite`.

* **[Layout Pages and Sections for Script and Templates](vfps://Topic/_4DS19CQWR)**  
You can now also create Layout or "Master" pages for scripts and templates that serve as 'common content' containers for content pages. Layout pages typically contain HTML header and common page chrome data - page headers, menus, sidebars that are common to many pages. Layout pages are called from Content pages via `<  % Layout="~/_layout.wc" %  >` which renders the layout page, which in turn renders the content page into a `<  %= RenderContent() %  >` area. Layout pages can also contain section directives to let the content page provide content to embed into the Layout page.

* **[Completely Rewritten Message Board](VFPS://Topic/_0190IC5ZN)**  
The message board sample application has been re-written from scratch as an MVC style application using the scripting features in Web Connection 6.0. This application serves as a full featured example for doing MVC style development with Web Connection.

* **[New Markdown Parser](VFPS://Topic/Class%20MarkdownParser)**  
Added new `MarkdownParser` and `MarkdownParserExtended` classes that allow parsing of Markdown text to HTML. Markdown is a popular text editing format for developer Web sites and CMS systems that simplifies text input via the simple markup formatting of the <a href="http://daringfireball.net/projects/markdown/syntax" target="top">Markdown Syntax</a>.

* **[jquery-resizable Plug-in](VFPS://Topic/resizable%20jQuery%20Plugin)**  
Added a jquery-resiable plug-in that makes it easy to create resizable components in HTML with JavaScript. Great for resizable dialogs, splitters or components that you want to resize.

* **[debounce() Function to throttle JavaScript events](VFPS://Topic/debounce)**  
Added a small function that allows 'debouncing' events to a specified number of milliseconds. Allows throttling high volume events like `window.resize()` or drag events to be held and fire only at specified intervals to avoid overloading and hanging the browser.

* **New Visual Studio 2015 VSIX Add-in**  
The Web Connection add-in has been updated to a full VSIX add-in that can be installed from Explorer by double-clicking. Functionality has also been updated to support View In Browser functionality for any type of HTML file including Web Connection Script files to make it easier to launch in the browser. If no configuration is provided the View in Browser feature still works with the default project configured URL.

* **[wwProcess::ResolvePath()](VFPS://Topic/wwProcess%3A%3AResolvePath)**  
Resolves a virtual path (~\) to a physical path on disk by replacing the virtual path specifier with the application path.

* **Allow Console.exe to run out of folders other than Wconnect Install Folder**  
Console.exe can now run from any location. When the console run it'll now explicitly change path to the /wconnect install folder and change back to the original folder when complete.

* **.NET Handler Faster COM Server Loading**  
Initial loading of COM servers when the servers are loaded has been improved significantly by removing a redundant load/unload cycle. Servers should now load twice as fast as previously.

* **[.NET Handler now allows Hotswapping of File Based Servers](VFPS://Topic/_1UZ0OXM7V)**  
You can now use File based messaging with the .NET Module and hotswap EXE servers. Like the COM Server feature, hotswapping allows dynamic updates of the server EXE file without shutting down IIS/Application Pools.

* **.NET Handler StatusPage Updates**  
The .NET module now supports ~\ virtual paths for TempPath and ExeFile paths, and the status page now displays the fully resolved OS path.

* **[ComValue::SetValueFromSystemConvert](VFPS://Topic/ComValue.SetValueFromSystemConvert)**  
Method that allows wwDotnetBridge to set a value from the .NET `System.Convert` static class in a simpler way. Allows access to all `System.Convert` methods.

* **[ComValue::SetUInt64 and SetUInt32 Methods](VFPS://Topic/ComValue.SetUInt64)**  
Added additional type conversions for UInt64 and UInt32 values.

* **[wwDotnetBridge::GetIndexedProperty()](VFPS://Topic/wwDotNetBridge%3A%3AGetIndexedProperty)**  
New method adds the ability to retrieve an indexed value from an IList based object like arrays and generic lists. 

* **[wwHtmlHelpers::HtmlDateTextBox()](VFPS://Topic/_4H90N6RCU)**  
New Html helper that renders the BootStrap DateTimePicker or optionally a native UI on mobile devices.

* **[wwHtmlHelpers HtmlDataGrid OnAfterRowsRendered Event](VFPS://Topic/HtmlDataGridConfig.OnAfterRowsRendered)**  
Added an event that is fired when all rows in the grid have rendered. You can attach an expression that can then render HTML to inject additional HTML like a footer.

* **[wwHtmlHelpers::HtmlErrorDisplay Updates](VFPS://Topic/_39213EUXL)**  
Updated HtmlErrorDisplay to use BootStrap alert themes and use font-awesome icons. Also added [HtmlErrorDisplayConfig](VFPS://Topic/_4GJ0LR1OQ) class that can be used to configure this method, as well as work as standard Web Connection form validation object.

* **[wwRequest::UnbindFormVars()](VFPS://Topic/_4FH15YHRR)**  
Added a new method UnbindFormVars() that's very similar to Request.FormVarsToObject() but differs in that it returns an collection of ValidationErrors message if there are binding errors.

* **[wwRequest::FormOrValue](VFPS://Topic/_4FT0OHOG7), [wwRequest::FormSelected](VFPS://Topic/_4GI0W95HP),[wwRequest::FormChecked](VFPS://Topic/_4GI0WTTQ9)**  
These new methods return value settings that can be embedded in script expressions into Web controls for `value=""`, `selected` or `checked` values effectively. These methods basically use a model value on GET requests or Request.Form() values on post backs to write out their values facilitating display of PostBack control state.

* **[wwRequest::GetMultipartFiles()](VFPS://Topic/_4H80RRA9J)**  
This method returns multiple uploaded files as a collection. This function support the `MULTIPLE` attribute on the File Upload input control by accessing multiple files from a single form variable.

* **[wwRequest::GetFormMultipleCollection()](VFPS://Topic/_4GK0RWOU0)**  
Like `GetFormMultiple()` but returns a collection instead of an array, to simplify working with the data more easily.

* **[wwRequest::GetFormVarCollection()](VFPS://Topic/_4GK0TET81)**  
Like `Request.aFormVars()` but returns a collection of objects with `Key` and `Value` properties.

* **[Response.ExpandScript()](VFPS://Topic/_1VO07HGB0) and [Response.ExpandTemplate()](VFPS://Topic/_S8B1DG4T7) Path Improvements**   
These two functions now allow using ~ in the path to specify the virtual root. Example: `Response.ExpandScript("~\testpage.wcs")` or     `Response.ExpandScript("~\views\login.wcs")`. You can also call either function without a parameter and it will use the template that references the Physical path of the request.

* **[wwProcess::OnAuthenticated() and wwProcess::OnShowAuthenticationForm()](VFPS://Topic/_2LH0X06QN)**  
Added additional features around authentication to make it easier to override the default authentication functionality. OnAuthenticated() is called when a user is authenticated either after logging in or after retrieving previous login information.OnShowAuthenticationForm() allows overriding the logic for displaying the authentication form so it's easy to hook in a custom form UI. The default form display now uses a customizable template so you might not have to modify code at all.

* **[wwRestProcess::OnBeforeCallMethod()](VFPS://Topic/_4L30QHJ33)/[OnAfterCallMethod()](VFPS://Topic/_S8X02FE4U)**  
Hook methods that allow you to hook into the REST process call pipeline before and after a REST method is called. Allows modifying global parameters (like JsonServer.IsDebugMode) or examine and modify individual requests.

* **[wwUtils.FlattenSql](VFPS://Topic/_4GL18I04C)**  
Flattens a multi-line SQL into a single line that FoxPro can execute at runtime using Macro expressions. Useful to allow you to write SQL with TEXT/ENDTEXT. Used internally in wwBusiness::Execute and wwBusiness::Query.

* **[wwUtils.IsNumber](VFPS://Topic/wwUtils%3A%3AIsNumber)**
Returns .T. if the provided value is a number, or a string that contains only digists and decimal/seperators (. and ,). Useful in many data binding scenarios to determine validity of number input.

* **[wwHtmlHelpers HtmlBindingError](VFPS://Topic/_4FH18RAAA)**  
This function can be used to display binding errors for input controls. By providing a control id and ValidationError collection errors can be displayed explicitly in relation to the related parent control.

* **[wwHtmlHelpers HtmlPager](VFPS://Topic/_4FM0T4NF6)**  
New HtmlPager() function that can be used to place a pager on the stage by specifying a page number, total pages and a base navigation URL.

* **[New wwJsonServiceClient Wrapper Class for Calling API Services](VFPS://Topic/Class%20wwJsonServiceClient)**  
This class provides an abstract `CallService()` method that automatically handles JSON serialization of parameters, making the HTTP call, and deserializing the JSON result back into a FoxPro class. The method makes the HTTP calls and handles all errors.

* **[wwJsonSerializer::FormattedOutput Flag](VFPS://Topic/_S8X02FE4U)**  
Added a flag to automatically format all output from Serialize() to be pretty formatted. This is a post-processing operation, so it adds some additional overhead, but can provide a nicer development experience.

<div class="updatenotice" style="margin-left: 0;margin-right">

<a name="v6breakingchanges"></a>

#### @icon-warning Breaking Changes in v6.0
* **Web Connection .NET Module compiled in .NET 4.0**  
We've changed the compilation mode of the .NET Module to .NET 4.0 which means that if you are running with .NET 2.0 on your server you need to switch (and possibly install) .NET 4.0. .NET 4.x is the default installed version on most servers and desktops today and 4.0 is forward compatible with 4.5 and later versions which improves stability and performance.

* **Legacy wwPageResponse Methods Removed**  
The default response class used in new projects is the **wwPageResponse** class. This class has been trimmed down, and has removed all legacy functions that modified headers, generated cursors and others. It removes the `ContentTypeHeader()`, `ShowCursor()`,`DbfPopup()`, `HtmlFooter()`,`HtmlHeader()` the various form control functions and more. If you are using an old application that uses these function you can switch to use the wwPageResponse40 class instead which is a subclass that includes all the legacy function. Override `wwProcess::cResponseClass` and specify **[wwPageResponse40](VFPS://Topic/_1O80YQ37Z)** and make sure you set the `#define INCLUDE_LEGACY_RESPONSEMETHODS .T.` in **wconnect_override.h**.

* **wwProcess::nPageScriptMode Default Change**  
The default setting of `nPageScriptMode` which determines how non-method loose script files with a mapped extension are processed has changed. The new default value is 3 - Scripts which uses `Response.ExpandScript()`. Other Values: 2 - Web Control Framework Pages, 1 - Templates (`Response.ExpandTemplate()`).

* **This is a test**  
Why can't this code just work. I can try and I can make this work when it does, but it's still quite difficult to know when things are working better or not.


* **wwProcess::ErrorMsg() requires an extra lcIcon Parameter**  
The ErrorMsg() function now receives an extra parameter to receive an Icon that is used from FontAwesome. If you are overriding the ErrorMsg() function in your own applications, make sure to add the extra parameter signature.

* **[wwJsonSerializer::Serialize "cursor:TCursor" now returns a raw Array](VFPS://Topic/wwJsonSerializer%3A%3ASerialize)**  
wwJsonSerializer now serializes cursors that are created with `cursor:CursorName` syntax to a straight array. Previously this generated an object with a `Count` and `Rows` properties while `cursor_rawarray:CursorName` generated a raw array. The `cursor:CursorName` syntax now has the same behavior as the `cursor_rawarray:CursorName` syntax, which still works, but it is deprecated in favor of the shorter and more logic syntax.

* **wwSession Table Length changed to VarChar(17)**  
We've added a new wwSession unique ID generation algorithm that generates longer and random ids which requires a wider field. Also changed the Fox and SQL types to VarChar so the data is no longer returned with trailing spaces. To update delete wwSession.dbf and let it recreate. For SQL either change the ID field to varchar(17) and update the Sql Scripts to reflect the new width or re-run the SQL Server configuration script from the Console.

* **wwDotnetBridge default .NET Version changed to .NET 4**  
Changed the default version that wwDotnetBridge uses if no version is specified to Version 4.0 (which includes all 4.x versions including 4.5 and 4.6). If you want to use .NET 2.0 explicitly pass `CREATEOBJECT("wwDotnetBridge","V2")`. We recommend you always specify your version **explicitly** anyway and remember only the first load determines the runtime loaded.

</div>