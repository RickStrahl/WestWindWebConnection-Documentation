## Version 8.3
<small>not released yet</small>

* **wwJsonSerializer Support for Output larger than 16mb**  
wwJsonSerializer can now serialize JSON to strings larger than FoxPro's 16mb string limit. While now supported it's still not recommended to generate JSON this large. It's both slow and eventually will hit FoxPro's internal memory limits. 

* **Fix: File Uploads with extended file names in wwHttp and wwRequest**  
Fix issue where filenames that contain extended characters where not properly UTF-8 encoded both when sending with [wwHttp.AddPostFile()](VFPS://Topic/_0JJ1AFXK3) and receiving on the Web Connection end in [wwRequest.GetMultipartFile()](VFPS://Topic/_S861BQM4E) and [wwRequest::GetMultipartFiles()](VFPS://Topic/_4H80RRA9J)

* **Fix: MemUsed Configuration Setting Applied in wwServer**  
The `MemUsage` configuration setting in `yourApp.ini` was not automatically read and used `SYS(3050,2,THIS.oConfig.nMemUsage)`. Uses this memory value or use 0 to bypass. Fixed.


## Version 8.2
<small>January 15th, 2025</small>

* **[wwDotnetBridge.GetField()](VFPS://Topic/_70Y19T8WR) and [wwDotnetBridge.SetField()](VFPS://Topic/_70Y19UN5Y)**  
Added explicit methods to get and set `public` .NET field values. It's not recommended that you access fields directly but we've added support for it. Note: Only works on `public` properties.

* **wwDotnetBridge .NET Event Optimization**  
Thanks to a pull request from Christof Wollenhaupt .NET events are now selectively bound, removing the requirement of having to bind all events. You now can implement as many of the events that you are using and no more.

* **[New wwHeap Class](VFPS://Topic/_73917NS6A)**  
Added a new wwHeap class that allows fast access to byte and character data including traversing the buffer byte by byte fast - unlike SubString. Useful if you're trying to build parsers or encoders where you need to read data character by character.


* **Fix: Old wwFtp Class Compatibility**  
Fixed a bug in the deprecated wwSftp class that caused download operations to fail.  
*We highly recommend you switch to the new `wwSftpClient` (Sftp)  or `wwFtpClient` (ftp or ftps) for future compatibility - the old wwFtp and wwSftp classes will not be updated in the future* 

### Version 8.1
<small>October 16th, 2024</small>

* **Add support for Response Output greater than 16mb**  
You can now - by default - return results that are larger than 16mb for both REST Services and HTML based output that uses the `Response` object including scripts and templates.

* **Fix COM Server Loading Timeouts in Large Instance Pools**  
Fix issue where large pools of COM servers would fail to load due to the server pool of instances timing out. Added a new `ComServerPerServerLoadTimeoutMs` configuration setting that estimates the average loading time for each server instance to calculate the timeout. The default is **2000** ms.


* **[wwPageResponse::WriteFullResponse()](VFPS://Topic/_70A17P0U8)**  
Added a new method that writes out a complete response, overwriting any previously written content. This is used internally to avoid concatenation of >16mb strings but can be used to explicitly write a full response.

* **New Size Field in the Logging Table**  
Added a new `Size` field to the logging table that logs the size of the Http Response in bytes *in any of the log modes that capture the response output*. When the response output is captured it's displayed in the log viewer in the top right corner along with date and request timing. 
**This is a potentially breaking change although the table will attempt to auto-update when using the FoxPro table. For SQL Server the `Size N(10,2)` field has to be added explicitly.**

* **wwUtils [SplitStringBySize()](VFPS://Topic/_709159HK5) and [JoinString()](VFPS://Topic/_70915M5CW) functions**  
Two new functions that help split strings by size which is useful for very large strings. SplitString() splits a string by size into a collection. 

* **[ComValue.SetValueFromCreateGenericInstance() with no Parameters](VFPS://Topic/_5C51DGO92)**  
Simplified this method so that the parameter list can be passed as `.NULL.` rather than requiring at least an empty parameter list.

* **[wwDotnetBridge.GetLoadedAssemblies()](VFPS://Topic/_70218VS3G)**  
New method that returns all assemblies that are loaded into the current process as a collection that includes name, location and fully qualified .NET name.

* **[wwDotnetBridge.ComArrayToCollection()](VFPS://Topic/_70218V2EX)**  
A helper method that returns a FoxPro collection from a ComArray instance. 

* **Fix: wwRequestLog Support for IPv6 Addresses**  
Updated the request log table definitions for `RemoteAddr` for 40 characters to fit IPv6 addresses.    
<small>*Requires that you widen the fields by deleting `wwRequestLog.dbf` and or re-running the SQL Server log table creation script*</small>.

#### Breaking Changes in v8.1{style="color: firebrick"}

* **Request Log Field Change: New Size `numeric (10,0)` field in Log Table**  
The log table structure has been updated with a new `Size numeric (10,0)` for FoxPro and  `[size] [int] NOT NULL DEFAULT 0,` for SQL field. For FoxPro tables this value *should update automatically* when you restart your server or you can delete the table and it will be recreated. For SQL Server you'll have to explicitly add the field. This is a potentially breaking change for existing applications that have logging enabled.

### Version 8.0
<small>June 25th, 2024 &bull;
[Release Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=9179) &bull; 
[Breaking Changes](https://webconnection.west-wind.com/docs/_s8104dggl.htm#breaking-changes-in-v8.0)
</small>  


* **Improved Web Connection COM Server Management**  
Major refactoring of how COM server loading and unloading is handled in the Web Connection Module and .NET Core Server. Instances now load much quicker with parallelized server loading and instances becoming available immediately after they load. Unloading now unloads server instances as well as any other processes that might have been orphaned due to a hard crash or other unforeseen IIS failure. We've fixed the server double-loading in busy server scenarios to further improved load - and unload - speed.

* **Improved Module and Middleware Logging**  
Also refactored how the module and .NET Middleware log in `wcerrors.txt`. We reduced the noise for standard logging mode so that you only see errors logged, rather than potentially non-critical information messages. Detail logging now logs a number of additional bits of request information with more specific error messages for a number of known COM errors. Additionally the log format has been cleaned up to be more consistent and removes a number of redundant messages.

* **[FTPS and updated FTP Support via new wwFtpClient class](VFPS://Topic/_6WP0MRZ80)**  
Added a replacement for the `wwFtp` class, that supports FTP and FTPS. The new class uses a .NET Library - Fluent FTP - to provide FTP functionality. The new API has been simplified to just a few simple functions with clearer naming than the legacy wwFtp class. 
* **[Refactored wwSftpClient Class](VFPS://Topic/_6WR0ZM6JD)**  
Added a replacement for the `wwSftp` class, that supports SFTP (FTP over SSH). This library has the same interface   and can be used interchangeably with the `wwFtpClient` class.

* **[wwRestProcess::InitTokenSession Bearer Token via wwSession Support](VFPS://Topic/_6R60ZS1WR)**  
Added a custom wwSession implementation that can be used to generate Bearer Token ids that are tied to a `wwSession` instance and can be validated via Bearer Tokens in the `Authorization: Bearer <token>` headers.

* **[wwRestProcess::Authenticate Authorization via Bearer Tokens](VFPS://Topic/_6R619BPU9)**  
Added support for authenticating users via Bearer Token authentication and tying it to `wwUserSecurity`. Authenticate can run in Signin, Validation and Signout modes and by default maps to standard Web Connection UserSecurity user management.

* **[wwRestProcess::lRawResponse](https://webconnection.west-wind.com/docs/_6vt0nn7e4.htm) for non-JSON Results**  
Added a helper property called `lRawResponse` to the `wwRestProcess` class that proxies to the `JsonService.IsRawResponse` property, to make the behavior more discoverable. This flag tells the service to not parse the result from the method into JSON and instead expects your code to send raw `Response` output.

* **[New wwZipArchive Class](VFPS://Topic/_6WW0Y5KQQ)**  
Added a new wwZipArchive class that provides better control over zip functionality using modern, native .NET functionality that removes the old dependency on Dynazip libraries. The new class provides the ability to zip and unzip folders, files, wildcards, append and get entry listings and retrieve individual files selectively.

* **[wwDotnetBridge.GetPropertyRaw()](VFPS://Topic/_6VO0PU6U2) and [ComArray.ItemRaw()](VFPS://Topic/_6VO0P9YVG)**  
Overridden methods that allow for retrieval of property values in raw format that bypass the usual FoxPro fixups that ensure type safe values are returned to FoxPro. Useful in scenarios where the values are sometimes in ComValue or ComArray that can be accessed directly, or in scenarios where types have dual meaning (ie. char with raw number vs. string fixup or Guid with raw binary vs. string fixup).

* **[ComArray.GetInstanceTypeName()](VFPS://Topic/_6VO0OEM1H) and [ComArray.GetItemTypename()](VFPS://Topic/_6VO0OF6WL) helpers**  
Added a couple of helpers to the ComArray class to provide type information about the Array instance and it's client types for debugging or testing purposes. This can be useful to determine whether the `.Instance` member can be accessed directly via FoxPro code (many .NET collections cannot and require intermediary operations provided by ComArray or wwDotnetBridge).

* **[wwDotnetBridge::DisposeInstance() to explicitly release Object Dependencies](VFPS://Topic/_6WZ0YZHBV)**  
This method explicitly release `IDisposable` object instances. This is a helper that makes easier to call `Dispose()`  on objects that don't implement this interface at the exposed class level.

* **New wwCache Methods**  
Added two new methods to wwCache: `Clear()` that clears the cache and closes the underlying cache cursor to avoid excessive memo bloat and `GetOrAddItem()` that combines retrieving an existing value, or setting a new one into the cache.

* **Move Web Connection Web Server to .NET 8.0**  
Updated the Web Connection Web Server to run the latest version of .NET Core 8.0. This brings up the version to the latest .NET Runtime which also boasts a number of performance and resource usage improvements. The Web Connection Web Server can be used standalone and can be shipped with your application to run locally on client machines and it effectively allows you to deploy 'local' Web applications, as well as supporting running efficiently inside of IIS in a Windows Server environment.

* **Add .NET Hosting Bundle Runtime  Installer**  
Adding a .NET runtime installer - `DotnetRuntimeInstaller.exe` that can quickly install the .NET Hosting Bundle required to run the Web Connection Web Server. Checks to see if already installed and if not optionally downloads and installs the required version. Can also download the .NET 8.0 Desktop runtime via the `-desktop` command line switch. This installer is automatically called from Setup if choosing the Web Connection Web Server. Effectively this automates the .NET Runtime installation if you choose to use the Web Connection .NET Web Server for local development (recommended). As a reminder this server is self-contained and you manually start it as part of your `Launch()` sequence to run your app locally.


#### Breaking Changes in v8.0{style='color: firebrick'}
* **The wwFtp and wwSftp classes have been deprecated**  
They are still available and now ship in the `classes\OldFiles` folder. They are replaced by the `wwFtpClient` and `wwSFtpClient` classes that provide similar functionality with a more consistent interface. If you were using the `UploadFile()`/`DownloadFile()` methods the new classes are compatible with identical interfaces.

* **wwFtp::OnFtpBufferUpdate() and wwSFtp::OnBufferUpdate()**  
The signatures for the FTP events handler for the **wwFtp** and **wwSFtp** classes have changed using a unified interface that now works the same both for the old and new FTP classes. If you are using these event handlers with wwFtp or wwSFtp make sure that update to the new signatures.

* The following libraries have to be updated:  
  * A new `FluentFtp.dll` is required for `wwFtpClient`
  * The `Renci.sshNet.dll` has been updated to the latest version
  * An update to `wwDotnetBridge.dll`  is required for all FTP functionality

### Version 7.40
<small>February 25th, 2024</small>

* **wwJsonSerializer Deserialization Performance Improvements**  
Optimized the .NET parsing of the deserialized object graph for improved performance. Also fixed a few small issues that previously could result in naming conflicts that FoxPro couldn't deal with. Fixed a small issue with UTC dates when `AssumeUtcDates` (ie. passthrough as-is dates) is set.

* **wwJsonServiceClient CallService Url Fix up**  
You can now use a site relative URL by specifying a `cServiceBaseUrl` before calling `CallService()` which allows you to use site relative paths for the URL. You can use Urls like `/authenticate` which makes it easier to switch between different host sites. If the URL does not start with `http://` or `http://`, the `cServiceBaseUrl` is prepended to the URL to create a full URL. This is useful if you switch between different sites such as running against different servers for dev, staging and production servers.


### Version 7.35
<small>November 15th, 2023 &bull; [Release Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=9175) &bull; [Breaking Changes](https://webconnection.west-wind.com/docs/_s8104dggl.htm#breaking-changes-in-7.34)</small>

* **[wwDotnetBridge: Improved support for Task Exception Handling](VFPS://Topic/_5PJ0XL2YP)**  
When making calls to .NET `async` or `Task` methods, wwDotnetBridge now does a better job of handling exceptions and returning the result in the `OnError()` callback. More errors are handled and error messages should be more consistent with the actual error (rather than a generic error and an innerException).

* **Updated to latest .NET `Newtonsoft.Json` Library for Json Serialization**  
We've updated the .NET JSON parsing library to ensure continued compatibility with the latest .NET versions widely used. It's been a while since the last update, and `v13.0.3` has been in circulation now for quite a while and this gets us up to date with the latest performance and parsing improvements of Newtonsoft's JSON library. 
**This is a potentially breaking change: Make sure to update the `Newtonsoft.json.dll` and `wwDotnetBridge.dll` in your projects.**


* **wwJsonServiceClient: Optionally capture Request and Response Data** 
You can now optionally capture all request and response data via the `lSaveRequestData` flag. If set any POSTed JSON data will be capture in `cRequestData` and any result data is capture in `cResponseData` both of which are useful for debugging.

* **wwJsonServiceClient is abstracted into its own PRG File**  
wwJsonServiceClient now has migrated out of the `wwJsonSerializer.prg` file to its own `wwJsonServiceClient.prg` file. This is a minor breaking change - you'll need to make sure `DO wwJsonServiceClient` is called explicitly now to ensure the library is loaded along with all dependencies.

* **UpdateNoComRegistration Module Configuration for bypassing COM Registration on Publish**  
Added this switch to allow bypassing COM registration when uploading and hotswapping new binaries to the server. This switch can be useful if you're using the same server for multiple Web Connection installs, especially when publishing file based projects that are used in tandem with COM based instances.

* **Fix: COM Server Publish Servers not Reloading**  
Fixed issue where COM server uploads and restarts would not work correct

* **Fix: Stop wwDotnetBridge from Attempting to Serialize Non-FoxPro Objects**  
wwDotnetBridge now checks for COM objects and components and won't serialize those objects rather than attempting and failing. FoxPro's `AMEMBERS()` can't reliably retrieve properties from COM objects so rather than getting an incomplete object, or worse a crash due to non-properties being access, we now serialize `null` for COM objects.

#### Breaking Changes in 7.35{style='color: firebrick'}

* **Make sure to update `Newtonsoft.Json.dll` and `wwDotnetBridge.dll` in your Projects**  
The two dlls are linked to each other and so they always need to stay in sync in order for JSON serialization to work consistently. Needed if you use JSON deserialization using `wwJsonSerializer`, or you're building API Web projects.

### Version 7.32

<small>April 5th, 2023 &bull; [Release Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=9173)</small>

* **[GetUrlEncodedKeyCollection()](VFPS://Topic/_6L215PRBT) to retrieve all QueryString and ServerVariables**  
You can use this new method to create collection from a set of Url encoded key value pairs, such as those found in the `.cQueryString` and `.cServerVariables` properties as well as explicitly Url encoded strings. 

* **[wwDotnetBridge COM Array Support for Lists, Dictionaries, Collections](VFPS://Topic/Class%20ComArray)**  
Added support for direct access of all sorts of collections directly in `ComArray` instances which are returned for collection results from `InvokeMethod()`, and `GetProperty()` or when creating lists. You can now access `List<T>` and `Dictionary<TKey, TValue>` collections directly with ComArray, via `Item()`, `AddItem()`, `RemoveItem()` etc. All methods have been updated to work with `IList`, `IDictionary`, `ICollection`, `Array` and `IEnumerable` with support for numeric indexes or key or value lookups.

* **Auto-Fixup of ComArray for Instance Parameters in all Intrinsic Methods**  
You can now pass ComArray instances directly as the first `loInstance` parameter for most intrinsic invocation methods like `InvokeMethod()`, `GetProperty()`, `SetProperty()` etc. Thanks to [Christof Wollenhaupt](https://github.com/cwollenhaupt) for this improvement and bug fix around a regression error related to the new list and dictionary support. [wwDotnetBridge #27](https://github.com/RickStrahl/wwDotnetBridge/issues/27)

* **ComArray.Add() method to complement AddItem()**  
There's now a `ComArray.Add()` method that performs the same task as `AddItem()` but provides better native compatibility with .NET collection syntax (ie. `List.Add(loItem)`). This method **only supports a single parameter** used for Lists and non-dictionary collections. For dictionaries with keys use [AddDictionaryItem()](VFPS://Topic/ComArray.AddDictionaryItem) to an item with a key.

* **wwJsonSerializer no longer uses PropertyExclusionList on EMPTY Object**  
When serializing `EMPTY` objects, or by association cursors and collections which internally use `EMPTY` objects, the `PropertyExclusionList` is not applied to properties. The list is meant to keep FoxPro default properties from polluting the output JSON, but EMPTY objects do not have any base properties, so the list is not necessary. This allows for creating properties with reserved FoxPro property names like `Comment`, `Name`, `Classname` etc.

* **Fix: wwJsonSerializer::AssumeUtcDates Output still converting to Local**  
Fixed issue that when this flag was set, it would not convert the inbound date from local to UTC but use the current date as UTC, but it would still convert the date back to local when deserializing. This change now leaves the deserialized date in the original UTC time, but returns it as a local FoxPro time (ie. the date is not adjusted for timezone) which was the original assumption of this flag. This was broken when we switched from FoxPro based parsing to .NET parsing using JSON.NET. **This is a potentially breaking change if you used this obscure flag in your code**.

### Version 7.30

<small>October 25th, 2022</small>  

* **Improved Support for .NET Core in wwDotnetBridgeCore**  
Improved runtime discovery logic for wwDotnetCoreBridge. Fixed `GetDotnetVersion()` to show .NET Core version.

* **wwDotnetBridge: [GetwwDotnetCoreBridge()](VFPS://Topic/_6G50UTL8E) and [InitializewwDotnetCoreBridge()](VFPS://Topic/_4X60SEGBF)**  
Added these helper functions to load the .NET Core Runtime of wwDotnetBridge and caching the runtime in the same way that the Full Framework versions of these functions do.

* **[wwCookie](VFPS://Topic/_5M50NOCS1) and [Response.AddCookie()](VFPS://Topic/_1O80YQ3EY) Default Changes**  
Made default cookie handling more secure by adding `HttpOnly` to the default settings. Also added `samesite=strict;` to the default when explicitly creating a cookie. The `wwCookie::Init()` constructor now can set the name and value ie.: `CREATEOBJECT("wwCookie","name","value")`

### Version 7.28

<small>June 12th, 2022</small>

* **Added [wwJsonSerializer::DefaultEmptyDate Property](VFPS://Topic/_6920VLWCN)**  
Added `DefaultEmptyDate` property to wwJsonSerializer to allow you set a predefined date value for empty dates since JSON doesn't have the notion of the *empty date*. The default date uses JavaScript's default zero date which `1970-1-1` which was the previous value set by default and couldn't be overridden.

* **Fix: wwJsonService UTF-8 Encoding/Decoding**   
Fixed inconsistencies in UTF-8 encoding by the service client. Now data sent is encoded and data received is decoded. Optional parameters allow disabling this auto en/decoding.

#### Breaking Changes in 7.28{style='color: firebrick'}

* **wwJsonServiceClient fixes UTF-8 decoding by default**  
There was a bug that caused `wwJsonServiceClient` to not decode UTF-8 content automatically, which would result in extended characters in deserialized values and objects to be misformatted. In 7.27 content now is UTF-8 decoded by default and you can disable the auto-decoding via the `lNoUtfDecoding=.T.` to keep the old behavior.

### Version 7.26

<small>January 18th, 2022 &bull; [Release Post](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=9170)  &bull; [Breaking Changes](#breaking-changes-in-7.26)</small>

* **JSON Server Status**  
The Web Connection Handler and Web Connection Web Server now have a dedicated `AdministrationJson.wc` link you can use to retrieve server configuration information as JSON. The result includes all the server configuration settings as well as information about the running server similar to the main `Administration.wc` page.

* **Web Connection Web Server now runs on .NET 6.0**  
Updated the local Web Connection Web Server to run on the newly released .NET 6.0 which improves performance and provides a number of useful enhancements to the local web server operation. .NET 6.0 is an LTS release. **This is a breaking change that requires that .NET Core 6.0 is installed**.

* **Add Performance Counters back to Web Connection Server**  
The .NET Core Web Server now display CPU usage for server instances on the Middleware Administration page. This was previously not working.

* **Better Error Handling for REST Service Invalid URLs**  
Invalid REST Routes - ie. referencing a missing method in a REST Service - now returns an HTTP 404 error. The error message now also describes the error better as `Missing endpoint method: MethodName`.

* **[wwJsonSerializer::MapPropertyName()](VFPS://Topic/_67X0KWDFD)**  
Method that allows you to map an individual property name to a new name. This allows you to map names to values that standard FoxPro property serialization would not normally allow for such as property names with spaces or special characters.  Similar to `PropertyNameOverrides` in behavior but with more control.

* **Automatically clear wwSql Named Parameters before new command executes**  
Automatically clear SQL parameters set on the last command, before executing the next command, unless `lNoParameterReset` is explicitly set to avoid this. This removes the need to explicitly clear parameters when running multiple commands on a single instance/connection. This was previously implemented but lacked the `lNoParameterReset` flag to allow to keep parameters for multiple commands for unusual use cases where the same command is run many times in a row. Note parameters are cleared **before the next command runs** not when the original command has completed, so you can review parameters and also receive out parameters.

* **Fix: Issue with Live Reload Requests not Firing on IIS**  
Fixed issue where hitting a link on IIS/IISEXPRESS occasionally would not work when LiveReload was enabled. Fixed by ensuring the output stream is properly disposed before writing the injected reload script.

* **[Fix wwSftp::OnFtpBufferUpdate()](VFPS://Topic/_67N0SA4AA) to allow Cancelling Downloads and Uploads**  
Fix `OnFtpBufferUpdate()` for `wwSftp` so it now works to provide to check for the `lDownloadCancelled` flag on a passed in `loSftp` instance when set. Previously this flag was ignored.

* **Fix: ExpandTemplate with _Layout Pages not reading Base Path correctly**  
Fixed issue where `wwPageResponse.ExpandTemlate()` was not correctly setting the Web site base path resulting in empty layout pages to be rendered. This in turn resulted in empty pages. Fixed.

#### Breaking Changes in 7.26 {style='color: firebrick'}

* **NewtonSoft.Json.dll needs to be Updated**  
We've updated the .NET JSON serializer to the latest version `13.0.1` in Web Connection and if you're using the REST API Process classes or using the JSON Serializer in any way make sure to update this DLL in your application folder or `DeserializeJson()` might fail.

* **Web Connection Web Server now uses .NET Core 6.0**  
The optional West Wind Web Connection local Web Server now uses .NET 6.0 to support the latest LTS release of the .NET Core Runtime. To use this local Web Server you'll need to make sure that [.NET Core 6.0 SDK](https://dotnet.microsoft.com/download/dotnet/6.0) or [.NET Core 6.0 Hosting Bundle](https://dotnet.microsoft.com/download/dotnet/6.0) is installed.


### Version 7.25
  
<small>October 1st, 2021</small> &bull; [Breaking Changes](#breaking-changes-in-7.25)

* **Improved wwSmtp Error Reporting (.NET mode)**  
wwSmtp now reports error information using the base exception rather than the top level exception which provides more info for connection level errors such as socket connect failures or invalid connection information/domain names.

* **[Improved access to HTTP and Serializer functionality in wwJsonServiceClient](VFPS://Topic/_4JF1F19ZR)**  
Refactored the `wwJsonServiceClient` to always create the the `.oHttp` and `.oSerializer` properties on startup. These objects allow customization of the HTTP request and JSON Serializer respectively. A new `AddHeader()` method is a shortcut method to make HTTP Header access more discoverable.

* **[wwEncryption::ComputeHash()](VFPS://Topic/_4C10W1PS4) BinHexMode parameter**  
Added a parameter that lets you specify the output format as **binHex** optionally which is a double character representation of each byte in the result hash. The default format is **base64** and the `llUseBinHex` flag optionally overrides to let you use **binHex**. If the parameter is not specificied the global `SetBinHexMode()` setting is used instead.

* **Refactored wwEncryption::[EncryptString()](VFPS://Topic/_4C10W1PRR)/[DecryptString()](VFPS://Topic/_4C10W1PRX)**  
These methods now let you choose the crypto provider (TripleDES and AES), the cipher mode, IV (for various ciphers) and optional key hashing modes. You can also set the binHex mode optionally.

* **wwUtils: ShowJson() Function**  
Added a new `ShowJson()` function in wwUtils that shows the content of the string in the configured JSON editor (typically VS Code). Also update the ShowHtml, ShowXml methods to output UTF-8 files that are then rendered from disk to avoid garbage characters otherwise shown.

#### Breaking Changes in 7.25 {style='color: firebrick'}

* **NewtonSoft.Json.dll needs to be Updated**  
We've updated the .NET JSON serializer to the latest version `13.0.1` in Web Connection and if you're using the REST API Process classes or using the JSON Serializer in any way make sure to update this DLL in your application folder or `DeserializeJson()` might fail.


### Version 7.22 
<small>August 6th, 2020</small>  

* **[wwRequest::QueryStringMultipleCollection()](VFPS://Topic/_6260PQ6K2)**  
This new method retrieves multiple values (multi-select) for a single key in a query string. 

* **Add support for AES Encryption in wwEncyrption Class**  
You can now use AES encryption optionally instead of the default TripleDES encryption previously supported for the `EncryptString()` and `DecryptString()` methods. You can now also create encrypted content that **does not** encode the provided encryptionKey with MD5 (as is common) and instead uses the raw encryption key to allow matching encrypted content generated by tools other than this method.

* **[wwJsonSerializer::DeserializeCursor()](VFPS://Topic/_3N51EFR1D)**  
New helper function that wraps `Deserialize()` and `CollectionToCursor()` which deserializes into a cursor/table that's already open to provide the data structure to import to.

* **Local User Credentials for SMTP Emails**  
Added support for a special `LOCALACCOUNT` username value that can be used to specify to use the logged on user's Windows credentials for SMTP server access. Note this is unusual and typically only works for internal servers running Exchange or other Managed Mail platforms using Windows Domain Accounts for access.

### Version 7.20
<small>March 16th, 2021</small>  
[Release Notes](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=9166) &bull; [Breaking Changes](#breaking-changes-in-7.20)

* **[Web Connection Web Socket Support](VFPS://Topic/_600118EDC)**  
Web Connection now supports basic Web Socket functionality that allows capturing Web Socket requests in an `OnWebSocket()` process class handler, as well as broadcasting messages to all connected Web sockets. Using a custom message format and a hybrid WebSocket and HTTP protocol it's possible for FoxPro to easily use Web Connection hosted Web Sockets to use two-way messaging. We provide a JavaScript `westwind-websocket.js` library for the client and a Web Connection Web Socket message class to create and send messages, as well as a wwProcess class `OnWebSocket()` handler method that can be overridden to intercept Web Socket messages in Web Connection FoxPro code.

* **[Consolidated Web Connection Administration Page: `Administration.wc`](VFPS://Topic/_sb4193pb9.htm)**  
Web Connection now has a single **built-in** administration that doesn't need to be explicitly distributed with an application. This page consolidates both the core Web Connection Module operations as well as most `wwMaint` operations that operate on the Web Connection server. The single page consolidates all functionality to a more user-friendly single page, and uses a single authentication experience and removes the need for distributing `Admin.aspx` or similar page.

* **Admin Page Updates**  
The `Administration.wc` admin page now includes an **Edit button** when running on the local machine under an Interactive account. If you use IIS Express, or the Web Connection Web Server you can now directly jump into editing the configuration for the appropriate configuration file.

* **wwDotnetCoreBridge .NET 5.0 Support**  
We've updated wwDotnetCoreBridge - the version of wwDotnetBridge that works with .NET Core - so that it now works with .NET (Core) 5.0. This is in addition to support for .NET Core 3.1. This update fixes some issues due to framework changes in 5.0 so make sure to update `wwipstuff.dll` and `wwDotnetBridge.dll` for existing installs.

* **[New wwProcess::OnRouting() handler](VFPS://Topic/_5UP0UZZQP)**  
New routing override hook method that allows you to customize process class routing which usually maps to a method in the process class or a page on disk to render. This mechanism intercepts the routing process before Web Connection's routing runs and lets you check for custom route conditions and call a Process method in response if the route matches. Very useful to allow re-using existing authentication functionality for without having to copy and create new process classes, or for handling static content that needs to be authenticated. The big benefit is that you can re-use a single process class's configuration.

* **New wwHttp::AddPostFile() Method**  
Added an explicit method that lets you add File Upload POST data for multi-part form based uploads. This is just a specialized version of the `AddPostKey()` method provided to make it explicit when uploading a file as part of form POST operation and to simplify the documentation.

* **WWC_LOGGING_MAX_CONTENT_SIZE Compilation Flag**  
This flag allows you to control the max Request and Response size to log when using Full Request or Request and Response logging. This allows you to control the size of the output captured to be limited in case of file up or downloads that may be very large. The default is 50,000.

* **Fix: Large Number JSON Result Values**  
[Fixed issue](https://support.west-wind.com/Thread5W1099FY1.wwt#5W20W5Y0Q) with large number rounding errors that would break JSON serialization. Caused by numeric overflows in FoxPro's value to string conversions, fixed by explicitly rounding down numbers to the `SET DECIMAL` setting.

* **Fix: Request.GetCurrentUrl() with Ports and HTTPS**  
Fix problem with `https://` requests that have custom ports not showing the HTTP scheme properly. Fixed.

#### Breaking Changes in 7.20{style='color: firebrick'}

* **Web Connection Web Server now uses .NET Core 5.0**  
To provide support for the most current version of .NET the Web Connection local dev server now uses .NET Core 5.0. To use this local Web Server you'll need to make sure that .NET Core 5.0 SDK or .NET Core Runtime and ASP.NET Core Runtime [are installed](https://webconnection.west-wind.com/docs/_5lw0ysxq9.htm#requires.net-core-5.x-runtime-or-sdk).

### Version 7.15
<small>August 2nd, 2020 &bull; [Release Notes](https://west-wind.com/wconnect/Weblog/ShowEntry.blog?id=963) &bull; [Breaking Changes](#breaking-changes-in-7.15)</small>

* **[New Log Formats](VFPS://Topic/_1Q40SZXAA)**  
You can now configure the Web Connection `wwWebRequestLog` via a new `nLogFormat` configuration setting: **0 - No Logging**, **1 - Minimal Request** (URL only, no request or response output saved), **2 - Full Request** (saves server vars and POST data but not Response), **3 - Request and Response** (saves everything). The setting can be set in `yourApp.ini` via `LogFormat=3` or using `Server.oConfig.nLogFormat`. This replaces the now obsolete logging compiler flag which allows the log levels to be changed at runtime, without recompilation. The format also removes the `SaveRequestFiles` setting which is now equivalent to logging with `LogFormat=3`.

* **[Updated Recent Request Viewer](VFPS://Topic/_1Q40SZXAA)**   
The **Last Request Viewer** has been updated to use the log information from the `wwRequestLog` table. The viewer has been updated with a new UI that shows the last 35 requests (can be changed). You can now quickly browse through multiple requests and not just the last one as the data is captured in the `wwRequestLog` table and loaded from there. For best log action during development set `LogFormat=3` - other formats show a note and recommend to use this format.

* **Change over to Build.ps1 from Build.bat for Project Packaging Script**  
Updated the Web Connection packaging build script to use Powershell to create a more generic script that is more portable. The build script packages the EXE, INI, dependent DLLs, a cleaned up Web folder (minus compiled files) and the data folder to provide a single zip file. Requires a 7Zip installation.

* **Start-FoxPro-IDE-in-Project.ps1 Script**  
To make it easier to generically launch the Web Connection development environment for a project the new project now generates a `Start-FoxPro-IDE-in-Project.ps1` Powershell script. This is in addition to the shortcut which also works in the project folder, but breaks if the project is moved. The Powershell script always works as it can use relative paths, rather than the required full paths in the Shortcut. You can just *Run with Powershell* to launch your project in the FoxPro IDE.

* **Session Cookies are set with `SameSite=Strict` Policy**  
Session cookies in Web Connection now use `HttpOnly` and `SameSite=Strict` policy to work around tracking requirements by Web browsers. Using `strict` forces the cookie to be confined to the originating site which means it's not shared with external services ensuring that data is kept private and preventing cross site scripting attacks.

* **wwCookie now defaults to `SameSite=Strict` Policy**  
As with Session Cookies, the default wwCookie class which can be used to create new cookies default to `SameSite=Strict` in order to comply with privacy requirements by default. If you need to share cookies with calling sites you can explicitly override the `.SameSite` property.

* **Server Hot Swapping File Uploads also Upload a DLL file if available**  
The two hotswapping routines now also upload a DLL file of the `UpdateExe` file, if that DLL file exists. This is to support VFP 10 build applications which are made up of a small loader EXE and the natively compiled FoxPro dll.

* **wwDotnetBridge support for non-Property Indexers in GetPropertyEx()**  
You can now access Indexed properties (`[0]` or `["name"]`) without requiring a property as part of the property name. So you can now do: `loBridge.GetValueEx(loDataRow,'["name"]')` for example.

* **Fix: JSON Date Formatting Rounding Issue**  
Fixed a bug where dates from cursors would not show the exact second/millisecond format with a float rounding error. Reworked the Json conversion function to adjust date explicitly to UTC format and then using `TTOC(ldDate,3)` instead.

#### Breaking Changes in 7.15{style='color: firebrick'}

* **Must update wwDotnetBridge.dll**  
wwDotnetBridge has some important fixes and a few additional helpers that are used by some of the libraries - specifically `wwJsonSerializer` which break when used with the older DLL.

* **wwRequestLog Structure Changes**   
The `wwRequestLog` has a few changes in fields so if you're using FoxPro tables, **delete the `wwRequestLog` table** and let it recreate. For SQL Server remove the table and use the Console SQL Log Table creation Wizard to let it recreate or use the `Westwind.sql` template script on an existing database.

* **Recommended: Set the `LogFormat`**  
The new `LogFormat` property is used to control logging. The default format is `1 - Minimal Request`. But for local development it's often useful to set this to `3 - Request and Response` so you can easily review requests and the output generated while debugging.

### Version 7.13
<small>May 29th, 2020</small>

* **[Request.GetServerName() now Returns Port Information](VFPS://Topic/_S850QHU8J)**   
In light of using IIS Express and the Web Connection Web Server which are using local port numbers as part of the URL, `Reqest.GetServerName()` has been updated to return domain names with the port **if not port 80 or 443**. It now returns `localhost:5200` for example or `localhost:7000`. This also fixes `Request.ResolveServerUrl()` when generating URLs for sites that are using port numbers.

* **[Request.IsLinkSecure() now checks the `HTTPS` server variable](VFPS://Topic/_S850QHUBL)**  
Previously `IsLinkSecure()` was checking explicit port numbers, but in recent IIS and ASP.NET Core versions an explicit `HTTPS` variable is available to determine precise status on whether a request is using a secure connection. This also fixes `Process.ResolveServerUrl()` losing the `https` prefix for URL generated.

* **[wwUtils `ExecuteCommandLine()`](VFPS://Topic/wwUtils%3A%3AExecuteCommandLine)**  
Added a new command line execution command that makes it easier to execute external executables using ShellExecute. Simplified syntax to take a single command line as string. Supports OS path resolution also works with Application Protocols

* **Administration Script Compilation Improvements**   
The new `Administration.wc` admin page now includes script compilation in the **Logging and Server Administration** section of the administration page. The script compiler now takes an extension or a comma delimited list of multiple extensions that can be used to recompile scripts in the application. 

* **[wwDotnetBridge::ToString()](VFPS://Topic/wwDotNetBridge%3A%3AToString)**  
Added a helper method to reliably return the result from .NET's internal `.ToString()` method. `ToString()` tends to be overloaded and so is not directly accessible on most via COM interop. This helper ensures it'll work on all .NET types.

* **Fix: Console Execution fails when not running out of Web Connection Install Folder**  
Fix issue with the Console application failing when started from within a project folder rather than the Web Connection root. Updated so that path is changed to the install root so all dependencies are found. This fixes a few odd problems like console files not being found and an errant `wconnect.fxp` file being created.

* **Fix: Web Connection Web Server Cookie Processing Bug in ASP.NET Core**  
Work around an ASP.NET Core bug that incorrectly formats the raw cookie string of a request by explicitly rewriting all cookies using the proper format.

### Version 7.12
<small>April 29th, 2020 &bull; [Release Notes](https://west-wind.com/wconnect/Weblog/ShowEntry.blog?id=959)</small>

* **[Console Update Project Resources Helper](VFPS://Topic/_0R10SLEAN#updateprojectresources)**  
Added a new `UPDATEPROJECTRESOURCES` Console Command that can be used to update an existing project with the resources from an updated Web Connection installation. This updates the Web Connection Modules, the Web Connection Web Server, Web Connection dependent Dlls (wwDotnetBridge, wwipstuff, json), the stock libraries, the Web Connection support scripts. Also added this to the main Console Form as UI operation that prompts for project path and operations to perform. This should make much easier to update existing project with the latest bits from a Web Connection.

* **Template Updates**  
Updated the stock Web Connection page templates to the latest builds of Bootstrap and FontAwesome. Also simplified some dependencies reducing script count.

* **Feature Parity for .NET Core Web Connection Web Server**  
The new .NET Core based, standalone  Web Connection Web Server is now 99% compatible with the classic ASP.NET handler so these two servers can be used interchangeably without feature differences. For development, the Web Connection server is very light weight and command line driven and can be automated easily.

* **Linux Support for new Web Connection Web Server**  
Because the new Web Connection Web Server runs .NET Core, it can also run on Linux which makes it possible to run the Web Connection Web Server on a Linux machine. Note that FoxPro **still has to run on Windows** but the server interface can run on a Linux box and push message files into a shared file location that both the Linux server and Web Connection Server application can access.

* **[New wwServer::ProcessHitBinary()](VFPS://Topic/_5QY1A8UPD)**  
Due to problems with encoding in ASP.NET Core related to Unicode string conversions there's now a new `ProcessHitBinary()` method on the server that receives a BLOB as input for request data, and returns BLOB data for output. This leaves all the string decoding and encoding predictably to the FoxPro end and lets the COM server simply process the raw binary data directly. This also removes an extra conversion and should remove some additional memory usage that previously was required for the Unicode string conversion. Both the ASP.NET Handler and .NET Core Middleware now call this method.
*This is a breaking change that requires that `webconnectionmodule.dll` and `wwServer.prg` are updated in sync as the new method is required.*.

* **[wwUtils IsDotnetCore() Helper](VFPS://Topic/wwUtils%3A%3AIsDotnetCore)**  
Support function that checks if .NET Core is installed. Returns the highest version number.

* **[wwWebServer::IsWebServerInstalled()](VFPS://Topic/_5PU0OQFKN)**  
Helper function that checks to see if the given Web Server type is installed and ready for configuration. Use the standard Web Server keys ("IIS","IISEXPRESS","WEBCONNECTIONWEBSERVER", "APACHE") to check if a given Web Server is installed. Internally used by the Console.

* **wwDotnetBridge Constructor Support for Structs**  
Fix ability to instantiate .NET `struct` types which now are returned as `ComValue` instances.

* **wwDotnetBridge Instances can now pass ComValue instances as Parameters**  
You can now pass `ComValue` instances to `InvokeMethod()`, `SetProperty()` and `GetProperty()` so that you can invoke functionality on objects that are otherwise not accessible in FoxPro. When you pass `ComValue` for the `loInstance` parameter, wwDotnetBridge automatically uses the `Value` property.

#### Breaking Changes in v7.12{style='color: firebrick'}

* **Update `webconnectionmodule.dll` and `wwServer.prg` due to `ProcessHitBinary()` Change**  
The new binary transfer functionality in this version is a major breaking change that requires that **both the `webconnectionmodule.dll` and the Web Connection framework (and more specifically `wwServer.prg` and `wwServer::ProcessHitBinary()`) are synced**. The module now calls this new `ProcessHitBinary()` method in `wwServer`Server interface so this method has to exist and it didn't in older versions. In turn this also adds the method to the COM interface, so make sure to **re-register your server** when updating applications on your servers.

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

#### Breaking Changes in v7.10{style='color: firebrick'}

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