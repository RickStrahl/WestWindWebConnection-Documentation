Web Connection automatically configures itself for operation with Visual Studio when you install Web Connection. Web Connection does the following:

* Installs a Web Connection Addin to Visual Studio
* Installs Web Connection Page Templates for Add New Project Item
* Installs a set of Code Snippets with a prefix of `wc-`

If you need to install the Web Connection Visual Studio features after the initial `Setup.exe` was run you can explicitly perform this task using the Console in the Web Connection install directory from within the FoxPro IDE:

```foxpro
DO CONSOLE WITH "VSNETCONFIG"
```

or use the Web Connection Menu and **Configure Visual Studio**. 

> #### @icon-warning Visual Studio 2019 Editor Mappings Not Registering
> Visual Studio 2019 and later now stores configuration settings in a private registry hive that can't be modified externally. Therefore Web Connection no longer sets default editor associations. Please install editor mappings manually.
>
> In Visual Studio use **Options->Text Editor->File Extensions**, then add your extension and map it to **Html Editor** or **Web Forms Editor** (for Web Control Framework Pages only). [More info below](#EnablesupportforCustomExtensions).



This utility configures all of the configuration settings required. However, depending on your Visual Studio configuration it's possible that some settings may not be set up completely. The most common problem is that the Web Connection controls fail to register properly on the Visual Studio Toolbox and you may have to add them manually as described below.

### Manual Visual Studio Configuration
This topic describes the complete process for manual configuration as well as a couple of the steps that cannot be automated. The following are covered:

* [Copy WebConnectionWebControls to the bin directory of your project](vfps://Topic/_1lo02ggfg)
* [Enable WCSX or custom script maps](vfps://Topic/_1LO02GGFG)
* [Adding Web Connection Page and User Control Templates](vfps://Topic/_1LO02GGFG)
* [Adding the Web Connection FoxPro Source View Add-in](vfps://Topic/_1LO02GGFG)
* [Configure the FoxPro Project BasePath for the Add-in](vfps://Topic/_1LO02GGFG)

### Copy WebConnectionWebControls.dll into the bin directory
<small>*occurs by default when you create a new project*</small>
In order for Visual Studio to be able to see Web Connection specific controls you need to copy the WebConnectionWebControls.dll file into the BIN directory of your Web application. This is a file that won't be used by your application, but Visual Studio needs in order to display the Web Connection controls on the toolbox and in the designer with all the custom Web Connection control properties.

### Enable support for Custom Extensions
Each extension must be manually configured once in Visual Studio so Visual Studio recognizes these extensions as Web Forms.

> #### Custom ScriptMap Extensions
>You can use **any** scriptmap extension for your script classes. Web Control framework pages can automatically be accessed if a page does not exist in your wwProcess subclass. For simplicity I'm going to use WCSX here, which is the default scriptmapped engine supported by Web Connection, but you can map any process class to a scriptmap and then use this same process for that script extension.

* In Visual Studio under Tools | Options | Text Editor Extensions add WCSX (or your Process Class' scriptmap - .dp shown here) 
* For Script or Templates Pages use **Html Editor**
* For Web Control Pages set it to **Web Forms Editor**

![](/images/misc/VisualStudioFileExtensionsOptions.png)


Note that the New Project and Process Wizards in Web Connection automatically add WCSX and the custom scriptmap configured for your process into the provided web.config file so this step is optional. If you decide later to add additional script maps or change an existing map in your application these maps have to be manually configured by adding the editor association.


### Adding Web Connection Page Control Templates
Web Connection also ships with a set of templates that are available for C# Web Pages and templates. These templates provide a blank page layout for you with the wwWebPage control already placed in the page so you don't have to drop it manually.

You can copy these templates in an automated way by doing:

```foxpro
DO CONSOLE WITH "VSNETCONFIG"
```

inside of your Web Connection install directory or by running the *Configure Visual Studio* option from the Web Connection Menu.

These files are installed for you automatically when you create a new Web Connection project, but if you need to manually copy the files you can move:

```
<wconnect_InstallDir>\VisualStudio\PageTemplates\*.zip
```

to (swap the VS version for the version you are using 2012,2015,2017,2019 etc.)

```
%homepath%\Documents\Visual Studio 2019\Templates\ItemTemplates\Visual Web Developer
```

Once these templates have been moved they will show up on the Add New Item option in Visual Studio. Important: Make sure you are **using  C# as your language**. 

### Adding the Web Connection FoxPro Source View Add-In
The Web Connection Source View Add-in attaches to the HTML editor's context menu and the Visual Studio **Tools** menu. It provides two core features:

* Jump to FoxPro Source File (if configured)
* View Page in Web Browser

The CONSOLE and Web Connection menu can automatically install the Add-in for you with:

```foxpro
DO CONSOLE WITH "VSNETCONFIG"
```

To manually install the Addin:

* Goto the `<WebConnectionInstall>\Visual Studio` folder
* Double click the `WebConnection-Addin.vsix` file

If the addin is already installed you will get a message dialog to that effect. If that's the case you can uninstall it from Visual Studio extensions.

You can also install the addin from the Visual Studio Gallery:

* [Web Connection Addin for Visual Studio](https://marketplace.visualstudio.com/items?itemName=RickStrahl.WestWindWebConnectionVisualStudioAdd-in)


> #### Addin works only in Visual Studio 2017 and Later
> We've removed support for the old addin that worked in Visual Studio 2010-2015. The new addin supports version 2017, 2019 and newer versions of Visual Studio.

### Configure the FoxPro Base Path for the Add-in
In order for the Add-in to find the FoxPro source file it needs to know the base path of the FoxPro application, so it can properly find the file and open it. The Source File contains a relative path to the current FoxPro IDE instance and so this path needs to be stored somewhere. The webConnectionVisualStudio section in the web.config file should have a section like this:

```xml
<webConnectionVisualStudio>
  <add key="FoxProjectBasePath" value="c:\WebConnection\Fox\" />
  <add key="WebProjectBasePath" value="c:\WebConnection\Web\wconnect60\" />
  <add key="WebProjectVirtual" value="http://localhost/wconnect60" />
  <!-- Optional PRG launched when VFP IDE launches -->
  <add key="IdeOnLoadPrg" value="" />
  <!-- The default browser used. Blank IE Automation, otherwise specify browser exe path -->
  <add key="WebBrowser" value="C:\Program Files (x86)\Google\Chrome\Application\chrome.exe" />
  <!-- The editor used to edit FoxPro code - blank means FoxPro Editor is used -->
  <add key="FoxProEditor" value="" />
  <add key="FoxProEditorAlternate" value="C:\Program Files\Sublime Text 3\sublime_text.exe" />
</webConnectionVisualStudio>
```

The Add-in reads the FoxProBasePath key out of the web.config file and then uses this path plus the project relativve path of the page to open the appropriate FoxPro source file. 

Along the same lines the WebProjectBasePath entries are used to find the Web directory and then is used to launch the browser in this directory. This is the directory and virtual name of the directory where your Web files (.wcsx or your scriptmap) live.
V
The paths are automatically set when you create a new project with the new project Wizard. Make sure that you adjust these values if you move your project or otherwise change paths.

For more detail on Add-in configuration and the values you can set in the configuration see the [Visual Studio Add-in Configuration](vfps://Topic/_27M0X8YX2) topic.

### A complete Web.config file
For completeness sake, here's a complete Web.config for a Web Connection project. You can use this for reference.

```xml
<?xml version="1.0"?>
<configuration>
  <configSections>
    <section name="webConnectionConfiguration" type="System.Configuration.NameValueSectionHandler,System,Version=1.0.3300.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    <section name="webConnectionVisualStudio" type="System.Configuration.NameValueSectionHandler,System,Version=1.0.3300.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
  </configSections>
  <appSettings>
    <add key="enableSimpleMembership" value="false" />
  </appSettings>
  <webConnectionConfiguration>
    <!-- NOTE: These settings apply only to the Web Connection Managed Module! -->
    <add key="MessagingMechanism" value="File" />
    <add key="Timeout" value="90" />
    <add key="TempPath" value="~\..\deploy\temp\" />
    <add key="TempFilePrefix" value="WC_" />
    <add key="ComServerProgId" value="Vue.VueServer" />
    <add key="ComServerLoadingMode" value="LoadBased" />
    <add key="ServerCount" value="2" />
    <add key="AutoStartServers" value="False" />
    <add key="ExeFile" value="~\..\deploy\Vue.exe" />
    <add key="UpdateFile" value="~\..\deploy\Vue_Update.exe" />
    <add key="AdminAccount" value="ANY" />
    <add key="AdminPage" value="~/admin/admin.aspx" />
    <add key="LogDetail" value="False" />
    <add key="ValidateRequest" value="False" />
    <add key="MessageDisplayFooter" value="Generated by Web Connection IIS .NET Connector Module" />
    <!-- Live Reload Settings -->
    <add key="LiveReloadEnabled" value="False" />
    <add key="LiveReloadExtensions" value=".wcvue,.wc,.wcs,.html,.htm,.css,.js,.ts" />
    <add key="UseStaComServers" value="False" />
    <add key="UseCustomManifestLoading" value="False" />
    <add key="AuthenticationMode" value="Default" />
    <add key="PassAspNetContext" value="False" />
  </webConnectionConfiguration>
  <webConnectionVisualStudio>
    <!-- Configuration Settings for the Web Connection Visual Studio Add-in 
       Not used at runtime, only at the design time -->
    <add key="FoxProjectBasePath" value="C:\WebConnectionProjects\Vue\deploy\" />
    <add key="WebProjectBasePath" value="C:\WebConnectionProjects\Vue\Web\" />
    <add key="WebProjectVirtual" value="http://localhost/Vue" />
    <!-- Optional PRG launched when VFP IDE launches -->
    <add key="IdeOnLoadPrg" value="" />
    <!-- The editor used to edit FoxPro code - blank means FoxPro Editor is used -->
    <add key="FoxProEditorAlternate" value="%LocalAppData%\Programs\Microsoft VS Code\Code.exe" />
  </webConnectionVisualStudio>
  <system.web>
    <compilation defaultLanguage="c#" targetFramework="4.6.1" />

    <!-- ASP.NET Request Limit Configuration for POST buffer and Script Timeout -->
    <httpRuntime maxRequestLength="10000000" executionTimeout="120" requestValidationMode="2.0" targetFramework="4.6.1" />
    <!-- Web Connection Managed Module requires that it runs in FULL TRUST (COM Interop) -->
    <trust level="Full" />
  </system.web>
  <!-- IIS 7 Script Map Configuration -->
  <system.webServer>
    <validation validateIntegratedModeConfiguration="false" />
    <!-- use this on IIS 7.5 and later to bypass IIS default Error handlers for failures -->
    <!-- httpErrors existingResponse="PassThrough" /-->
    <!-- IIS 7 Script Map Configuration -->
    <handlers>
      <!-- 
        Live Reload for HTML files
        **************************
        For **Development** uncomment the `".LiveReload_StaticHtml_wconnect-module"` below.
        For **Production** comment it so it's not loaded as it slows static file serving.        
      -->
      <!-- <add name=".LiveReload_StaticHtml_wconnect-module" path="*.htm*" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode" /> -->

      <add name=".wcvue_wconnect-module" path="*.wcvue" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode" />
      <add name=".wc_wconnect-module" path="*.wc" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode" />
      <add name=".wcs_wconnect-module" path="*.wcs" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode" />
      <add name=".md_wconnect-module" path="*.md" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" preCondition="integratedMode" /> 
    </handlers>
  
    <defaultDocument>
      <files>
        <clear />
        <add value="default.wcvue" />
        <add value="Default.htm" />
        <add value="index.html" />
      </files>
    </defaultDocument>
    <!-- 
      EXTENSIONLESS URL HANDLING
      - Requires IIS Url Rewrite Module 2.0 (Web Platform Installer)

      matches any URLs that do not have a '.' in the path    
  -->
    <!--
    <rewrite>
      <rules>

        <rule name="Redirect to HTTPS" stopProcessing="true">
          <match url="(.*)" />
          <conditions>
            <add input="{HTTPS}" pattern="^OFF$" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}{URL}" redirectType="SeeOther" />
        </rule>

        <rule name="Extensionless Urls" patternSyntax="ECMAScript" stopProcessing="true">
          <match url="^((?!\.).)*$" />
          <conditions>
            <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
            <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
          </conditions>
          <action type="Rewrite" url="UrlRewriteHandler.wcvue" appendQueryString="true" />
        </rule>

      </rules>
  </rewrite>
  -->
  </system.webServer>
</configuration>
```

The key elements are:
* Language to C#
* Add any script map extensions you want to edit as Web Forms/User Controls
* Make sure to add the FoxProjectBasePath App Setting so the Add-in can work can find your PRG files
* The WebProjectBasePath and WebProjectVirtual are used for View in Browser Add-in option and are required so that the .NET add-in can start the browser in the right directory.