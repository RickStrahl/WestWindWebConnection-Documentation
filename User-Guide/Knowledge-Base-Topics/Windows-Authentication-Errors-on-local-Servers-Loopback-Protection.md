*When logged into your Windows server, IIS Windows authentication through a browser does not work for either Windows Auth or Basic Auth using Windows user accounts. Login attempts just fail with a 401 error.*

*However, accessing the same site externally and logging in works just fine, using Windows log on credentials. It only fails when on the local machine.*

### Loopback Protection on Windows Server
This problem comes up on Windows Servers and lately also on Windows 10, or on Windows client machines running under custom policies. When attempting to log on locally on a local Web site using Windows account authentication the your username and password always fails when this policy is enabled. For Web Connection this affects the admin pages that rely on Windows authentication for access.

This problem is caused by a policy called **Loopback Protection** that is enabled on server OSs by default. Loopback Protection disables authenticating against local Windows accounts through HTTP and a Web browser.

For more info please see this Microsoft KB entry:  
<a href="https://support.microsoft.com/en-us/kb/896861" target="top">https://support.microsoft.com/en-us/kb/896861</a>

### Quick Fix: Disable Loopback Check
The work around is a registry hack that disables this policy explicitly.

Starting with Web Connection 6.21 and later you can run the following using the Console running as an Administrator:

```
c:\> console.exe disableloopbackcheck
```

To reverse the setting:

```
c:\> console.exe disableloopbackcheck off
```

To perform this configuration manually find this key in the registry on the server:

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa`

and edit or add a new key:

`DisableLoopbackCheck` (DWORD)

then sent the value to `1` to disable the loopback check (local authentication works), or to `0 ` (local authentication is not allowed).

Or more easily you can use Powershell:

```powershell
New-ItemProperty HKLM:\System\CurrentControlSet\Control\Lsa -Name "DisableLoopbackCheck" -Value "1" -PropertyType dword
```



> It looks like recent builds of Windows 10 (1803 and later?) also require this configuration setting in order to authenticate locally.