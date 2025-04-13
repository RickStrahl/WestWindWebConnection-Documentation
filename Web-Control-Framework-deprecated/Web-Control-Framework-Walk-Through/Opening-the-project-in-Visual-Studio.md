In the first step we created a new project and configured a Web Virtual directory, and configured it for operation with Web Connection. When you use Visual Studio for the first time with a Web Connection project you'll need to do a one time setup to configure extensions and the toolbox. Here's what you need to do.

> **Visual Studio Express Note:**  
> The free Express editions from Microsoft are stripped down ASP.NET specific version of Visual Studio and provides all the core features described here. However, it does not support Add-ins, so the features of the Web Connection Source and Browse View menu functionality is not available. We recommend you use the newer Visual Studio Community Edition which is a full version of Visual Studio Professional.

I'm going to use Visual Studio for my samples here since it provides visual editing support for the Web Controls that we'll be using. You can use any text editor to create the text though including Notepad, Sublime Text (which has a FoxPro plug-in from Matt Slay) and any other editor.

All versions of Visual Studio from VS2015 (as of this writing) down to Visual Studio 2008 are supported.

### Opening the project in Visual Studio
To start Visual Studio and open your directory follow these steps:

* Start Visual Studio
* File | Open | Web Site 
* Select File System and point at the directory of the Virtual created (d:\westwind\DevDemo)
* Click Open

Your Solution Explorer should look something like this once you'd done this:

![](IMAGES/WebControls/ControlDemoSolution_1.png)

Notice that the bin directory contains a WebConnectionWebControls.dll file. This file contains the Web Connection control definitions that map the FoxPro controls implemented in the Web Connection Framework. Although Web Connection works with some of the basic ASP.NET controls, it's best to use the custom controls as they map more closely to the features supported by Web Connection.

Let's create a new Web Page. Click on the project (the path) and right click select Add New Item. Make sure that Visual C# is selected as the target language to show the Web Connection Page Templates. If you installed a Web Connection project you can select a new Web Connection Page as shown below:

![](IMAGES/WebControls/NewPageDialog.png). 

In this case I'm creating a new file called Helloworld.dp. Remember .dp is the scriptmap we configured in the new project wizard and we're using a page with this extension. You can also use a .wcsx extension to get the generic version, while .dp is bound to our specific Process class. In general use the more specific version because it allows you to customize the behavior later on.

Using the Web Connection Template is best as it automatically adds some default controls to the page (wwWebPage specifically).  We'll look at what the page generated looks like in a second. 

Next: [Setting up your first Web Control Page](vfps://Topic/_1LY01HVR6)