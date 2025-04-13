The samples in the Web Connection 5.0 documentation show using the default generic WCSX extension for creating Web Control Framework applications. While this works just fine, if you use WCSX extension you are actually giving up a bit of configurability, because WCSX is mapped to a generic process class that you can’t explicitly override.

The preferred approach is to use a custom process class and a custom scriptmap like the .Blog extension for the WebLog for example. By doing so you get the ability to create custom logic into the wwProcess class, letting you override things like the stock error handling in OnError(), overriding OnProcessInit() for global processing you want to have happen on every request and setting properties on the Process object that customize operation such as default error page and so on.

If you create a new project with Web Connection this is automatically built into the new project – the scriptmap is configured and pointed at the new Process class. In Web Connection 5.0 any Process request that is made that cannot be matched to a method, goes to disk (actually to a cache cursor) and sees if there is a Web Control Framework page to process and if so processes it. IOW, the Web Control Framework is now built into any Process class you create.

There’s one kink though: Any extension you create must be configured in Visual Studio or Visual Web Developer so that it understands the extension and treats it as a Web Form. Here’s how:
<ol>
* In VS.NET 2005 under Tools | Options | Text Editor Extensions add WCSX or your custom scriptmap extension (like .Blog) as Web Form Editor 
<blockquote><small>**Extended Options:**  
Make sure that when you go to Tools | Options you always check the Show all settings checkbox. Some options like the extension mapping become available only after enabling this flag. This is especially true for Visual Web Developer.
</small></blockquote>

* Add a Web.config to the root of your Web directory
Then add the following to have VS.NET recognize your specific application extension: 
```xml
<configuration>
<system.web>
    <compilation defaultLanguage="c#" debug="true">
      <buildProviders>
         <add extension=".blog" type="System.Web.Compilation.PageBuildProvider" />
         <add extension=".wcsx" type="System.Web.Compilation.PageBuildProvider" />
      </buildProviders>
   </compilation>
</system.web>
</configuration>
```

</ol>

Web Connection will automatically create #2 when you create a new project or process with the Wizards as it creates a web.config file automatically. But the Editor configuration is a required manual step for each scriptmap you define.

### Already have a project using WCSX Extensions?
If you started out creating a new project with the WCSX extension don’t fret. You can easily use a custom class as well. Here’s what you do:
<ul>
* Create a new project or add a new Process class to an existing project. 
* Go through the Wizard as usual 
* When done, edit the <yourApp>Main.prg and the Process method 
* Find the WCSX mapping and point it at your process class:
CASE lcExtension = "WCSX"
      DO <MyProcess>.prg with THIS
</ul> 

And that’s it. At that point you can modify the MyProcess class and provide customized behaviors just like a custom scriptmap. Just be aware that now all WCSX requests route through this handler in this project.

When starting out from scratch it’s best to use a custom scriptmap from the start.