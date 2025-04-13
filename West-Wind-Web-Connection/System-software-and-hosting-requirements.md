Web Connection is designed as a server based application and thus requires a Microsoft Windows based server environment for ddeployment It can however run just fine on Windows 10,8,7,Vista and XP as well Server 2012 and 2008.

### Server Software
For development you can use a Windows client operaating system. For deployment a server version like Windows 2012, 2008 operating system is required due to Microsoft's licensing policies.

Web Connection Supports the following Web Servers:

* Microsoft IIS Version 6 and later
* Apache for Windows Version 2.0 and 2.2

The product is designed primarily on Microsoft Internet Information Server (IIS), which provides best performance, stability and the full range of features. It also works well with Apache 2.0 and 2.2 and can be used with any any other ISAPI compatible server on Windows. We do recommend IIS especially IIS 7 or later as it provides the most stable and compatible platform on which Web Connection is developed and tested.

### Development Software
Web Connection 6.0 is designed for **Visual FoxPro 9.0**.

Web Connection ships with some Visual Studio integration features including Visual Studio page templates, and a plug-in that allows opening the FoxPro editor from Visual Studio, preview scripts, templates and Web COntrol Framework pages in the browser of your choice.
You can use the  **<a target="top" href="https://www.visualstudio.com/en-us/products/visual-studio-community-vs.aspx">Visual Studio 2015 Community Editon</a>** which is free for all but big Enterprises. 

Otherwise you can use plain HTML editors with Web Connection to generate HTML and Templates. Tools like Sublime Text, WebStorm, Visual Studio Code,  Atom etc.  work just fine for HTML, CSS and JavaScript editing. However, I'd encourage you to check out Visual Studio as it provides a ton of HTML, CSS and JavaScript Intellisense help that surpasses other tools significantly in features.

### Hardware
Web Connection can run on just about any hardware, but obviously a current, fast machine is preferred. The software ran well 15 years ago on Pentium II machines and it can do so still today. Anything faster is just gravy. Essentially you'll want a machine that can run Visual FoxPro comfortably. 

For development we recommend the max amount of memory you can afford as it will make your dev environment work more smoothly. With memory under $10 a gigabyte, it's easy to justify running with 16 gigabytes of memory. Web development is very memory intensive especially if you use Visual Studio. Also a fast machine won't hurt for development as you spend a lot of time flipping back and forth between different applications (VFP, Web Browser, HTML Development Tool etc.).

For your development machine the biggest bang for your buck is likely to be an SSD drive which speeds performance significantly. If you don't have an SSD drive, you're missing the biggest performance gain you can add to your machine especially that prices for SSD have nosedived.

### Web Hosting
Web Connection must run on a Web Server and getting the application online means that you are connected to the Internet. There are a variety of ways available to do this from in-house hosting to co-location (your server at an ISP's facility) or plain Web hosting. 

#### VM Hosting
These days it's easy to set up a Virtual Machine to host an application on a hosted service like Microsoft's Azure, Amazon Web Services, Vultr or many others. You can pick any Windows hosting service to host your application and set up a new machine to go.

#### Shared ISP Hosting
We do not recommend shared hosting for Web Connection applications as configuration and setup on shared ISP services is generally a problem as Visual FoxPro has to be installed on the server and you need extended rights that are often not available for shared hosting ISPs. While we don't recommend it we provide guides and information on [how to set up Web Connection to run on low cost hosts](VFPS://Topic/_3C80PE77Z).

With VMs available at reasonable prices today, hosted VMs are a better choice for Web Connection applications.


#### In House Hosting
Finally your company may already have an internal Internet connection and you can hook your server application to one of your company servers on your internal network. In this situation you probably have to deal with your network administrator and any of the security policies configured for the company network. If your application is completely internal to your company (an Intranet) then a public Internet connection is not required. If your application is used both internally and externally (an Extranet) you will still need with the security issues of an open Internet connection through your IT staff.