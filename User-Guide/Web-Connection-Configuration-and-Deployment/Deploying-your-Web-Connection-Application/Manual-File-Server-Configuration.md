The Web Connection Management Console (CONSOLE.EXE) includes a [Server Configuration Wizard](vfps://Topic/_S7R16D1M4) which lets you configure a Web application and we recommend that you take advantage of this tool to configure your application.

However, if you want to manually configure your file based server after you've copied all files to the server here's how you do it. 

**Overview**  
Assuming you have copied your application to the Web server and you want to manually configure the server for File Based operation you have to configure the Web Connection application INI files so that the server and the wc.ini file can communicate with each other.

When working in Filebased mode the key thing is to make sure that your applications INI file (YourApp.ini or wcdemo.ini) is synched up properly with the Web Connection ISAPI INI file (wc.ini). Both need to point to the same directories for the temporary files so that they can communicate and the permissions on these directories need to be right.

**Changing the your FoxPro server's settings**  
Your FoxPro Web Connection Server uses an INI file with the same name as the project to hold a number of startup parameters. The easiest way to change the most common settings is to start the server and use the Status form.

To make changes to the application's INI file simply change the value on the Status form and click the Save Server Settings button. When you exist the form these settings should be live. These settings are written into your application's ini file (<yourApp>.ini or wcDemo.ini for the demo app). You can also set these settings manually in the INI file of course.

The key settings for file based communication are:

```
[Main]
Tempfilepath=d:\temp\wc\
Template=WC_
```

You can also use a path relative to the EXE's startup path by specifying ~\ to inject the startup file.

```
[Main]
Tempfilepath=~\temp\wc\
Template=WC_
```

will create a full path like:

c:\wwapps\wc3\webconnection.deploy\temp\wc\

where c:\wwapps\wc3 is my EXEs startup path. This can be useful for [self contained file based deployments](vfps://Topic/_3C80PLBOC).

**Changing the wc.ini settings**  
The Web Connection ISAPI INI file (wc.ini) contain configuration settings that tell the Web Connection web interface how to handle requests. This drives the C++ code and is separate from the INI settings above. 

To make changes to the Web Connection ISAPI INI file find wc.ini in your Web directory or the bin directory of your Web application (ie. \inetpub\wwwroot\webdemo\bin\wc.ini). Open the file with a text editor or the Visual FoxPro editor. 

<pre>[wwcgi]
Path=d:\temp\wc\
Template=wc_
</pre>

You'll want to change the Path and Template keys to match the value from your FoxPro server's INI file mentioned above.

**Changing Web Connection Module web.config Settings**  
If you're using the .NET module configuration settings are stored in web.config and the <webConnectionConfiguration> section.

```xml
<webConnectionConfiguration>
  <add key="TempPath" value="d:\temp\wc\" />
  <add key="TempFilePrefix" value="WC_" />
</webConnectionConfiguration>
```

You can also use tilde syntax to specify a path relative to the Web root. So:

```xml
<webConnectionConfiguration>
  <add key="TempPath" value="~\webconnection.deploy\temp\wc\" />
  <add key="TempFilePrefix" value="WC_" />
</webConnectionConfiguration>
```

where the ~ is expanded to the base path of the site like c:\inetpub\wwwroot\wconnect. Again this is useful for a self contained installation that can be deployed to a low cost ISP.

**Setting Permissions on the Temp folder**  
You need to make sure that SYSTEM, the Internet Guest Account (IUSR_<machinename> or whatever you have configured in IIS as the Anonymous User) and Administrators have FULL rights in the temp directory used in the entries above.

**Starting up and checking settings**  
Once this is done you should be able to start up your Web Connection Server from Visual FoxPro or as an EXE. Try a request - it should work fine from here.


### But it's not working

![](IMAGES\misc\TroubleShootingFileBased.png)

To trouble shoot a file based installation please check out the Troubleshooting a [Filebase Server Installation](vfps://Topic/_S8X00DY2J) topic.