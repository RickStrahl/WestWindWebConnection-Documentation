When running Web Connection applications under IIS, you are most likely not loggged in as a specific user and there is no user console active. Instead IIS runs under a system context that is a custom logon that isn't directly bound to user control.

This means if you rely on drive mappings in your applications, drive mappings that you make under your regular user account are not going to be visible in the IIS session directly. 

This also means that you have to explicitly map drives. There are a couple of ways of doing this.

### Use UNC Paths
UNC paths are direct network references to paths on the network. So rather than using mapped drive letters you provide the full network file location.

```foxpro
use \\server\share\folder\data.dbf
```

There are several problems with this approach. First and foremost there's no way to specify login information so if your remote drive requires authentication with username and password this will not work. It'll only work when the network login matches rights on both machines. 

Additionally in my experience UNC access to files is often drastically slow than mapped drives. I'm not sure why this happens and in researching I've found lots of conflicting information on this topic. Experiment with both shared and UNC drives and compare performance before making a descision on either approach.

### Map your Drives on your application's Startup
You can also map a drive as part of your application's startup process. You'll want to do this as one of the very first things you do before you access any resources that live on the remote drive, preferrably in **YourServer::OnInit()**.

You can use the following helper function:
```foxpro
mapnetworkdrive("i:","\\RASXPS\CDrive","username","password")
IF (!IsDir("i:\temp"))  && point at some known path or file
    * ** Log this error into the startup trace log (wwTraceLog.txt)
    this.Trace("i: drive was not mapped...") 
ENDIF
```

### Use your Machine Policy to create a Startup Script
Windows supports creating startup scripts that are launched as part of policy. Keep in mind that IIS runs under a system context so typically user context is not loaded (ie. there are no startup and start tasks that fire) so using policy is what's required to ensure when a new user sessions is started that the scripts execute.

To do this:

<ul>
* Go Edit Group Policy from the Start Menu
* Local Computer Policy/Computer Configuration/Windows Setting/Scripts/Startup
* Create a batch file on disk where all accounts can access it
</ul>

![](IMAGES%2FGroupPolicyEditor.png)

In the batch file include your NET USE statements:

```foxpro
NET USE i: "\\server\data" "password" /USER:"username" /persistent:yes
NET USE h: "\\server\configuration" "password" /USER:"username" /persistent:yes
```

Note that you may have to include the domain name of the user if you are running on a Windows DOMAIN (ie. "DOMAIN\username").