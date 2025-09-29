Looking at this documentation you may be feeling overwhelmed with information overload. **Relax!** You don't need to understand nor even read everything that's in this documentation, but when you want to dig deeper and [understand some of the topics](VFPS://Topic/_S8104DGGL) the information is there waiting for you. This topic serves as a road map for your startup with Web Connection.

This topic is designed to summarize the things that you should most definitely look at. It gives you an idea on where to start, what to try to get going and where to look for more information.

Use the Help File! It's got tons of information, and if you get stuck the answer is likely in here. It's keyworded and searchable in addition to the Content view, so take advantage of this very rich resource. 

### Setup and Installation
The first step in getting Web Connection installed is to install the product from the self-executing ZIP file. Run the file and the following installation program. You can find more info on installation at this topic:

[Setting up Web Connection](vfps://Topic/Setting%20Up%20Web%20Connection)

### Checking out the demos
This step serves two purposes: It makes sure that the installation works and lets you see Web Connection in action with demos that you can look at and code you can step through if you choose. Make sure you run the Web Connection server (`DO wcDemoMain.prg`). 

<a href="http://localhost/wconnect/" target="top">Go to the demo page</a>

Most of the examples on the demo pages have links to show you the source code required to create that request by clicking on the [Show Code] link on the bottom of the page. At this time you can also start poking around in the code, making a few adjustments, maybe creating a new method and stepping through the code to get an idea what's involved in writing code for Web functionality.

### Walk through a Tutorial
Web Connection ships with several tutorials in the documentation that help you get started. There are four tutorials provided:

* **[Step by Step: Getting Started](VFPS://Topic/_0NB1AL6FM)**
* **[Step by Step: MVC Development with Business Objects](VFPS://Topic/_0I102WSAI)**
* **[Step by Step: Creating a JSON REST Service and Angular Front End](VFPS://Topic/_4IU1EV8PM)**

The first gets you started with Web Connection after installation and creating your first new project, and walks through some of the basic features. The second is a bit more advanced and dives into build MVC (Model View Controller) style applications that use script templates that mix HTML and FoxPro expressions and code to render UI and code to drive the templates. The final example demonstrates how to build JSON REST services and consume them in a variety of ways from FoxPro, plain HTML with jQuery and finally in a small Angular application.

### Use the Web Connection Management Console to create a new project
Once you have familiarized a little bit with how Web Connection works you're probably ready to create your own requests for your own application. To do this you can use the [New Project Wizard](vfps://Topic/New%20Project%20Wizard) by running the [Web Connection Management Console](vfps://Topic/The%20Web%20Connection%20Management%20Console).  To start the management Console type `DO Console.exe` into the command window or run the EXE from Explorer.

The new project will set up a new VFP application for you that you can start to write your own requests with. Now it's time to write access your business logic from within these Web requests and create some output to display back in the browser. As you start writing code you likely run into a few situations where you don't know how to do something. I suggest you go back to the demos and see if you can find something there that demonstrates what you're trying to do. Again, the <a href="http://www.west-wind.com/wwThreads/" target="top">message board</a> is a great resource to post questions to if you get stuck or even if you want to bounce some ideas of other developers who have been down the path.

### Use the Documentation, Luke
Once you start your own development you will want to keep this help file or the <a href="http://www.west-wind.com/webconnection/docs/" target="top">online version</a> handy. 

The documentation contains both an extensive user guide as well as a complete reference for all of the supported classes and components. There's a lot of it and the docs are huge - but don't think that you need to know all of it. You'll use small chunks of what's there frequently and many other only occasionally. Being productive with Web Connection really requires only a a small percentage of what is available in this powerful tool. But it's nice to have access to many of those features when the need arises.

The extensive class reference will come in very handy as you start creating requests using the Web Connection framework classes. Most of your development work involves a couple of objects - [ wwRequest](VFPS://Topic/_S850QFR2E), [wwPageResponse](VFPS://Topic/_1O80YQ37Z) and [wwProcess](VFPS://Topic/_S840SSNHB) - take a look at those first and keep them handy - you'll use those a lot.

### Configuration and Installation
Once you've built an application you will need to actually install it on a Web server or other server machine. Although this process is not difficult there are a fair number of files and components involved. The [Web Connection Configuration](vfps://Topic/Web%20Connection%20Configuration%20and%20Deployment) topic describes many of the configuration components and the options available in them.

You can also resort to the [Management Console](vfps://Topic/The%20Web%20Connection%20Management%20Console) and the [Server Configuration Wizard](vfps://Topic/Server%20Configuration%20Wizard) to help you with configuring your Web server with the appropriate virtual directories and script maps as well as copying and registering the Web Connection components for your application.