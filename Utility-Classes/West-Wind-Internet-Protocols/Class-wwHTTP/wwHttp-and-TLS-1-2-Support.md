TLS is the encryption protocols used for `https://` connections on the Web in general. TLS is implemented by Windows as part of the Windows Security Protocols stack. `https://` is required by many services that your application might be interacting with such as making HTTP calls to a Credit Card Processor, Mail Delivery Service or some other online API that your application interacts with.

TLS 1.2 is the current version of TLS and **is required** by many secure applications like banks and credit card processors. 

Unfortunately not all versions of Windows support this version of TLS 1.2 natively.

### Windows and TLS 1.2
TLS 1.2 is relatively new and old versions of Windows didn't have native support for TLS 1.2. Here's a rundown of how TLS 1.2 is supported in Windows versions: 

#### Built-in Support for TLS 1.2

* Windows 8.1,  Windows 10 or later
* Windows Server 2012 R2, Windows 2016 or later

These versions of Windows just work out of box with TLS 1.2 - no changes are required and it just works.

#### Configurable Support for TLS 1.2

* Windows 8
* Windows 7
* Windows Server 2012, 2008 R2

These versions can support TLS 1.2 via registry settings (for machine wide settings) or Internet Explorer Configuration of Protocols for IE and WinHttp/WinInet functionality. For more info on how to enable TLS 1.2 see below.

#### No Support for TLS 1.2

* Windows Vista
* Windows Xp
* Windows Server 2003
* Windows Server 2008

These older versions of Windows **don't have support TLS 1.2 at all** and don't have any way to make it work outside of using applications that don't use the Windows Protocols Stack. 

This also affects the `wwHttp` class which runs through WinInet in Web Connection or the Client Tools.

### More Info in Blog Post
There's a lot more detail on what works and what doesn't and  how to configure those versions of Windows that can be configured to use TLS 1.2 in this blog post:

* [Web Connection and TLS 1.2](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=937&id=937)