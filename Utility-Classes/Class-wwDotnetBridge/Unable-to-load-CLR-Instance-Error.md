If you get the error:

**Unable to load wwDotnetBridge: Unable to load Clr Instance. 0x80131515**

it means wwDotnetBridge wasn't able to load an instance of the self-hosted .NET runtime and won't be able to continue. 

### Loading from a Network Location
A common problem is related to .NET security for DLL's loaded **from a network share**. If you load `wwDotnetBridge.dll` or run your application from a network share, .NET treats the network loaded DLL as coming from a different **low rights Windows Security Zone** which - by default - is then not allowed to execute.

There's a simple fix for this by creating a `MyApp.exe.config` (for your custom EXEs) or `VFP9.exe.config` (for the VFP IDE) in the same folder as the EXE. This effectively adds a **Runtime Policy** to allow loading assemblies from network paths:

```xml
<configuration>
  <runtime>
      <loadFromRemoteSources enabled="true"/>
  </runtime>
</configuration>
```

This effectively makes networked DLLs behave the same as native Windows executables and simply load. .NET 4.5 executables have this policy automatically enabled via their Application Manifest, but wwDotnetBridge and FoxPro call into the runtime via COM, so the policy has to be explicitly enabled via an external `.config` file.

### Blocked .NET DLLs: wwDotnetBridge.dll
Another common problem is that `wwDotnetBridge.dll` is **locked down** by Windows as an insecure Dll, **if installed from an Internet downloaded .zip file**. .NET 4.5 won't load an insecure DLL initially when running under User Account Control (UAC). 

> As of wwDotnetBridge 6.22 we have added an automatic check for blocked DLLs so this should no longer be a problem. However, if wwDotnetBridge fails to load however still check for blocked DLLs to make sure.

If you need explicitly unblock the DLL you can right click on the `wwDotnetBridge.dll` in Explorer and set the Unblock checkbox:

![](//images/misc/unblockwwdotnetbridge2.png)

Older versions of Windows 10 and prior Windows versions used a button instead:  

![](IMAGES/misc/UnblockwwDotnetBridge.png)

You can also use this Powershell command to unblock:

```Powershell
Unblock-File -Path '.\wwdotnetbridge.dll'
``` 

> **Note:** You have to restart FoxPro after unblocking the DLL.

#### Why does this happen?
If you installed the wwDotnetBridge zip file directly from an Internet download, `wwDotnetBridge.dll` might be blocked by Windows. If you are running with **User Account Control on**, the .NET Runtime will not be allowed to load an Internet downloaded loader DLL as it is deemed to be from the insecure Internet Zone.

> #### @icon-info-circle Blocking applies only to downloaded DLLs
> Windows applies blocking **only to executable files downloaded from the Internet** either directly or via a containing zip archive (such as wwClient.zip) and .NET respects this blocking by failing to load DLLs that are blocked. 


If you install `wwDotnetBridge.dll` as part as a **full installation program**, the file will be properly installed and is not considered **downloaded** and so no unblocking is required.

### wwDotnetBridge.dll and wwIPstuff.dll not Found
Make sure when you run any code that uses wwDotnetBridge, that `wwdotnetbridge.dll` and `wwipstuff.dll` **are in your FoxPro path**. 

If not sure, use an explicit check from your application's startup folder that tells you whether the DLLs are visible:

```foxpro
IF !FILE("wwdotnetbridge.dll") OR !FILE("wwipstuff.dll")
  MessageBox("ERROR: wwdotnetbridge.dll or wwipstuff.dll not found.")
  RETURN
ENDIF
```

We recommend that if the DLLs are not in your application's or dev environment's root path, you explicitly add the path where those DLLs live to your application like this:

```foxpro
SET PATH TO "<relativePathToDlls>" ADDITIVE
```

You can also check exactly where your app is finding those DLLS with:

```foxpro
? FULLPATH("wwdotnetbridge.dll")
? FULLPATH("wwipstuff.dll")
```


### .NET Runtime or requested .NET Runtime Version not installed
Another issue that might cause the runtime to fail to load is, if the .NET runtime is not present or an old or incompatible version is installed. 

The latest version of wwDotnetBridge requires:

* .NET 4.5 or later (Windows 7, Server 2008 R2 and later)

Windows 8 and later and Windows 2012 have compatible runtimes already installed. Windows 7 and Server 2008 require **explicit installs of later versions**.

You can download the latest version (or any other 4.5+ version) from here:

* [.NET Downloads - Choose .NET Framework](https://www.microsoft.com/net/download/all)

If you run into problems we highly recommend you install the **latest version of .NET Framework** available for Windows 7 or Windows Server 2008 R2 and later as this removes any ambiguity about versions. .NET Framework installs are relatively small and in most cases require no reboots (unless they are in use by system services).

#### Older OS Versions
For older OS versions (Vista, XP, 2008 (original), 2003) we also ship `wwdotnetbridge_xp.dll` which is compiled with .NET 4.0 and works on these old OS's. 

To use it, rename  `wwdotnetbridge_xp.dll` to `wwdotnetbridge.dll` and replace the newer version. This version will not load .NET 4.5 and later assemblies however, which is why we moved away from this version since many common .NET components require at least .NET 4.5 today. This includes shipped support libraries Markdig (Markdown parsing) and SSH (SFTP), but it will work for wwSMTP emails, JSON serialization and various support functions in Web Connection.

### Troubleshooting
If you have problems, it's often a good idea to run a very simple check to see if wwDotnetBridge is working. There are two ways to do this:

* Run the Version check
* Run our test executable

#### Checking what .NET 4.x Version is Installed
If wwDotnetBridge loads at all you can check the version and status information with this code:

```foxpro
DO wwDotnetBridge
loBridge = GetwwDotnetBridge()
? loBridge.GetDotnetVersion()
```

This will display the **full** .NET version number as well as the 'simplified' version number (ie. `4.6.1` or `4.7.2`).

If wwDotnetBridge doesn't run, you can also check which version is installed, but it's bit tedious and requires a registry check:

* [How to determine .NET Framework Version](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed)

#### Use our Test Sample
We provide a self-contained test executable that you can run in a self contained folder. It's a precompiled EXE that bundles matched DLLs and tries to load wwDotnetBridge and reports the version. Download and unzip this file into a clean, **local drive folder**, then **make sure you unblock `wwdotnetbridge.dll`*** after downloading as described above.

* [wwDotnetBridge Tester](https://west-wind.com/files/wwDotnetBridgeTest.zip)

#### Check your own Version
If you rather do this yourself with the version you have, you can do this from the FoxPro command window. Note **make sure you have a matched set of wwipstuff.dll and wwdotnetbridge.dll** and **that these DLLs are in your FoxPro Path**. 

Here are the steps to make sure you have a clean environment:

* Start a **fresh instance** of Visual FoxPro IDE
* `SET PATH TO  && clear path`
* `CD <folderWithwwDotnetBridge\wwIPStuff_samples\wwDotnetBridge>` 
* Then run the following code

```foxpro
IF !FILE("wwdotnetbridge.dll") OR !FILE("wwipstuff.dll")
  ? "ERROR: wwdotnetbridge.dll or wwipstuff.dll not found."
  RETURN
ENDIF

DO wwDotnetBridge
loBridge = GetwwDotnetBridge()
? loBridge.GetDotnetVersion()
```

This is the simplest thing you can do without any external dependencies.