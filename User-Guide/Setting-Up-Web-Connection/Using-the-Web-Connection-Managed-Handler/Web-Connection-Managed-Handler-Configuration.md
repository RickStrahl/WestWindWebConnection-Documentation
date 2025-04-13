The Web Connection Managed Module is configured through settings in the Web.Config file in the root directory of your Web application.

A full Web.Config looks something like this:

```xml
<?xml version="1.0"?>
<configuration>
  <configsections>
    <section name="webConnectionVisualStudio" type="System.Configuration.NameValueSectionHandler,System,Version=1.0.3300.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"></section>
    <section name="webConnectionConfiguration" type="System.Configuration.NameValueSectionHandler,System,Version=1.0.3300.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"></section>
    <section name="webConnectionErrorPages" type="System.Configuration.NameValueSectionHandler,System,Version=1.0.3300.0, Culture=neutral, PublicKeyToken=b77a5c561934e089"></section>
  </configsections>
	<webconnectionvisualstudio>
		<!-- Configuration Settings for the Web Connection Visual Studio Add-in -->
		<add key="FoxProjectBasePath" value="c:\wwapps\wc3\"></add>
		<add key="WebProjectBasePath" value="C:\westwind\DevDemo\"></add>
		<add key="WebProjectVirtual" value="http://localhost:81/DevDemo"></add>
		<!-- Optional PRG launched when VFP IDE launches -->
		<add key="IdeOnLoadPrg" value=""></add>
		<!-- The default browser used. Blank IE Automation, otherwise specify browser exe path -->
		<add key="WebBrowser" value=""></add>
		<add key="WebBrowserAlternate" value="c:\program files\mozilla firefox\firefox.exe"></add>
		<!-- The editor used to edit FoxPro code - blank means FoxPro Editor is used -->
		<add key="FoxProEditor" value=""></add>
		<add key="_FoxProEditorAlternate" value="C:\Program Files\pspad\pspad.exe"></add>
	</webconnectionvisualstudio>
  <system.web>
    <compilation defaultlanguage="c#" debug="false">
      <buildproviders>
        <add extension=".wcsx" type="System.Web.Compilation.PageBuildProvider"></add>
        <add extension=".wp" type="System.Web.Compilation.PageBuildProvider"></add>
      </buildproviders>
    </compilation>
    
    <trust level="Full"></trust>
    <httphandlers>
      <!-- pre IIS 7 or IIS 7 in non-integrated mode
           NOTE: you still need to set up scriptmaps to c:\windows\Microsoft .NET\v2.0.50727\aspnet_isapi.dll
      -->
      <!--<add verb="*" path="*.wp" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule"/>
			<add verb="*" path="*.wc" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule"/>
			<add verb="*" path="*.wcsx" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule"/>
			<add verb="*" path="*.wcs" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule"/>
			<add verb="*" path="*.wwsoap" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule"/>-->
      <!-- End pre IIS 7 -->
    </httphandlers>
  </system.web>
<system.webserver>
    <handlers accesspolicy="Script, Execute, Read">
      <!-- IIS 7 in Integrated Mode -->      
      
      <add name="*.wp_wconnect" path="*.wp" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" precondition="integratedMode,runtimeVersionv2.0"></add>
      <add name="*.wc_wconnect" path="*.wc" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" precondition="integratedMode,runtimeVersionv2.0"></add>
      <add name="*.wcsx_wconnect" path="*.wcsx" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" precondition="integratedMode,runtimeVersionv2.0"></add>
      <add name="*.wcs_wconnect" path="*.wcs" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" precondition="integratedMode,runtimeVersionv2.0"></add>
      <add name="*.wwsoap_wconnect" path="*.wwsoap" verb="*" type="Westwind.WebConnection.WebConnectionHandler,WebConnectionModule" precondition="integratedMode,runtimeVersionv2.0"></add>      
      
      <!-- end IIS 7 in Integrated Mode -->
      <!-- IIS 7 non-integrated mode ScriptMappings -->
      <!--<add name="wp_ISAPI" path="*.wp" verb="*" modules="IsapiModule" scriptProcessor="C:\Windows\Microsoft.NET\Framework\v2.0.50727\aspnet_isapi.dll" resourceType="Unspecified" />
      <add name="wc_ISAPI" path="*.wc" verb="*" modules="IsapiModule" scriptProcessor="C:\Windows\Microsoft.NET\Framework\v2.0.50727\aspnet_isapi.dll" resourceType="Unspecified" />
      <add name="wcsx_ISAPI" path="*.wcsx" verb="*" modules="IsapiModule" scriptProcessor="C:\Windows\Microsoft.NET\Framework\v2.0.50727\aspnet_isapi.dll" resourceType="Unspecified" />
      <add name="blog_ISAPI" path="*.blog" verb="*" modules="IsapiModule" scriptProcessor="C:\Windows\Microsoft.NET\Framework\v2.0.50727\aspnet_isapi.dll" resourceType="Unspecified" />
      <add name="wwd_ISAPI" path="*.wwd" verb="*" modules="IsapiModule" scriptProcessor="C:\Windows\Microsoft.NET\Framework\v2.0.50727\aspnet_isapi.dll" resourceType="Unspecified" />-->
      <!-- end IIS 7 non-integrated mode ScriptMappings -->
    </handlers>
  </system.webserver>
  <webconnectionconfiguration>
    <!-- NOTE: These settings apply only to the Web Connection Managed Module! -->
    <add key="Timeout" value="60"></add>
    <add key="PollTimeout" value="100"></add>
    <add key="InputPostBufferSize" value="65356"></add>
    <add key="PostBufferLimit" value="0"></add>
    <add key="TempPath" value="c:\temp\wc\"></add>
    <add key="TempFilePrefix" value="WC_"></add>
    <add key="MessagingMechanism" value="File"></add>
    <add key="AdminAccount" value="ANY"></add>
    <add key="AdminPage" value="~/admin/admin.asp"></add>
    <add key="ExeFile" value="c:\wwapps\wc3\WebDemo.exe"></add>
    <add key="UpdateFile" value=""></add>
    <add key="LogDetail" value="False"></add>
    <add key="ValidateRequest" value="False"></add>
    <add key="ComServerProgId" value="WebDemo.WebDemoServer"></add>    
    <add key="ComServerLoadingMode" value="LoadBased"></add>
    <add key="ServerCount" value="2"></add>
    <add key="AutoStartServers" value="False"></add>
    <add key="MessageDisplayFooter" value="Error generated by Web Connection IIS Connector Module"></add>
  </webconnectionconfiguration>
  <webconnectionerrorpages>
    <!-- NOTE: These settings apply only to the Web Connection Managed Module! -->
    <add key="Exception" value=""></add>
    <add key="OleError" value=""></add>
    <add key="Timeout" value=""></add>
    <add key="NoOutput" value=""></add>
    <add key="Busy" value=""></add>
    <add key="Maintenance" value=""></add>
    <add key="InvalidRequestId" value=""></add>
    <add key="TranmitFileFailure" value=""></add>
    <add key="PostBufferSize" value=""></add>
  </webconnectionerrorpages>
</configuration>
```

### configSections
Required to let ASP.NET know that there's a couple of custom configuration sections. These two sections contain the Web Connection Configuration settings and a set of error page templates. 

### webConnectionVisualStudio
These settings are actually not used by the module but rather by the Web Connection Visual Studio add-in and they hold several settings that determine how the add-
in operates and where it can find and store files.

### Compilation
The compilation setting sets the language to C# which is important only for the editor. Typically you'll want to set debug to false as well as it provides optimal performance for the ASP.NET application.

### BuildProvider
These settings are not  used by the module by but by Visual Studio which allows you to edit the pages visually.

### Trust Level
The Web Connection Module performs a number of high trust operations like writing files to non-Web paths, calling COM objects, starting and stopping processes and so requires Full Trust. This is not an optional setting. Along the same lines the module requires that the app runs under LocalSystem or an Admin account.

### Handler Mappings
The Handler mappings map a script map to the Web Connection module. The set up varies depending on whether IIS 7 in integrated mode or previous versions are used. You can review the [specific version topics](vfps://Topic/_22Q0YKDNS).

### webConnectionConfiguration
This section contains Web Connection's main configuration settings that determine how this application operates. See [webConnectionConfiguration Settings](vfps://Topic/_22Q13S5PA).