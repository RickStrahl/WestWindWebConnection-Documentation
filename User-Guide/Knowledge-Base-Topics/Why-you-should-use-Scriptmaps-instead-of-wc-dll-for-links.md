Scriptmaps are  a Web Server feature that map a physical executable file like the ISAPI extension (wc.dll) or the .NET Module (WebConnectionModule.dll) to a virtual file extension. For example Web Connection ships with a number of default file extensions for wc, wcs, wcsx etc.

Using a script map allows to use a more 'natural' naming mechanism with your dynamic Web pages:

Instead of:

**http://localhost/wconnect/wc.dll?wwDemo~HelloWorld**  

you can simply use:

**http://localhost/wconnect/helloworld.wwd**  

where the scriptmap `.wwd` is mapped to the WebConnection .NET module or wc.dll ISAPI handler in the Web server, and `.wwd` is mapped to the wwDemo process inside of the Web Connection server. 

Web Connection automatically understands that the extension of a scriptmap it knows about is the route to the process class (wwDemo in this case) and the stem of the file is the method to be called (Helloworld). This makes for cleaner and more readable URLs that are also portable.

### Why use Script Maps
There are a number of different reasons to use scriptmaps:

* **More Secure**  
IIS by default doesn't allow .dll files to be accessed directly from URLs so in order to use wc.dll you have to add disable a default filter setting. 

* **Cleaner Syntax**  
Scriptmaps make it easier to access URLs using cleaner URL syntax. Rather than using positional parameters in a url like  **wc.dll?wwDemo~HelloWorld**, you can use 'page syntax' where the page name and the scriptmap extension route the request: **HelloWorld.wwd**.

* **Scriptmaps are Path agnostic**  
Unlike DLL links which must be reference *wc.dll* in a specific path, Scriptmaps are path agnostic, which means they don't have to be referenced with a specific relative file location to be executed. You can use **/MyPage.wwd** or **/SubFolder/MyPage.wwd** and both will fire the same **MyPage** method. This removes the need for hardcoded paths and allows for more portable URLs that can be easily moved and copied without adjusting paths in your site.

* **Scriptmaps are portable**  
Scriptmaps can be configured in `web.config` and if you use the Web Connection Module they are referenced without a path, which makes the scriptmaps portable if your site moves to a new location - there's no explicit configuration required.

### How Scriptmaps work
The setup process for script maps varies depending on the server you install it on. Please check the [specific server configuration section](vfps://topic/_S8G0YEVJ0) for your server.

Web Connection scriptmaps are configured in web.config of your Web Server. The format varies depending on whether you use the .NET Module or the ISAPI extension.

#### .NET Module ScriptMap
For the .NET module you can configure scriptmaps like this:

```xml
<configuration>
 <system.webServer>
    <handlers>
        <add name=".wc_wconnect-module" path="*.wc" verb="*"
             type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule"/>
        <add name=".wwd_wconnect-module" path="*.wwd" verb="*"
             type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" />
    </handlers>
</configuration>    
```    

To create another scriptmap, just add another extension and change the name to a unique value (or you can use the Wizards to do it for you).

.NET Scriptmaps are fully portable and self-configured if you move your project to a new location since there's no file name provided. The scriptmap references a link in `WebConnectionModule.dll` which contains the `WebConnectionHandler` class referenced in the mapping.

#### ISAPI Scriptmap
For the .NET module you can configure scriptmaps like this:

```xml
<configuration>
 <system.webServer>
    <handlers>
      <add name="WC Handler" path="*.wc" verb="*"         
            modules="IsapiModule"
            scriptProcessor="C:\westwind\wconnect\wc.dll"         
            resourceType="Unspecified" preCondition="bitness32" />    
      <add name="WWD Handler" path="*.wwd" verb="*"
            modules="IsapiModule"     
            scriptProcessor="C:\westwind\wconnect\wc.dll" 
            resourceType="Unspecified" preCondition="bitness32" />
    </handlers>
</configuration>    
```    

The ISAPI mapping maps to a physical location of `wc.dll` typically in the `/bin` folder of your Web project. You also need to enable `wc.dll` in IIS to allow executing the dll.

### How Scriptmaps work
Scriptmaps are mapped at the Web Server to route to your Web Connection server. By assigning a script map as shown above you're telling IIS that when it sees a request to an extension like `.wwd` to route that request to your Web Connection server.

Once your Web Connection Server receives that request it further routes the request based on the extension. The `wwServer::Process()` method, handles routing and inside of that method a mapping exists for the script mapped extension.

For example:


```foxpro
*** Web Connection Demo handling
CASE lcExtension = "WWD" 
   DO wwDemo with THIS
```

This maps the `.wwd` extension to the wwDemo process.

The process class then looks at the filename and maps the filename of the request (ie. HelloWorld in HelloWorld.wwd) to a method. This is the default processing in Web Connection that handles the routing mechanism.

### Automatic Configuration available
Web Connection automatically configures scriptmaps when you use the [New Project Wizard](VFPS://Topic/_S7R0OXD9T), [add a New Process Class](VFPS://Topic/_S7R13T6G7) to your existing project, or you use `DO CONSOLE with "SCRIPTMAP"` [command line option](VFPS://Topic/_0R10SLEAN).