The first step in getting going with Web Connection is to install the product. Web Connection ships as a self-extracting zip file that you download from the Web.

### Unzip the Web Connection Install files
Click on the EXE file and unzip the application into a directory of your choice. The file is a WinZip self-extracting file, so it should ask you to either open the file or extract the content to a directory of your choice. I suggest you extract to a directory. This first step creates the Web Connection application directories, which are where all of the source and support files get installed.

Web Connection installs a hierarchy of directories as follows:

```txt  
c:\wconnect
	classes*     -	VFP source and direct support files for the framework (required for development)
	Web          -  HTML sample pages
	Templates*   -	Templates for new Web projects and various generators
	Tools        -	several useful support tools and classes
	VisualStudio -  Visual Studio Addin and Page Templates
	Samples      -	VFP source for the demos that ship with WWWC
```

> #### @icon-info-circle Before you start
> You need to [choose and install a Web Server](VFPS://Topic/_5U10P0FG7) to use with Web Connection and it has to be ready before installation. Setup checks to see if the server is available and if not links to the appropriate topic in the documentation.

### Run the Setup Program
The next vital step is to run the [Setup program](vfps://Topic/The%20Setup%20program) which is installed into the Application root directory. To run it, start Windows Explorer, go to the Web Connection install directory and double click on Setup.Exe. 

Please go through this topic in the documentation:

* [The Setup program](vfps://Topic/The%20Setup%20program) 

It takes you through the installation steps, shows how to start up the server and run the sample application.

### Go through the Getting Started WalkThrough
Once you've installed Web Connection and maybe have played with the sample application, you'll probably want to create a new project. To take you through these steps please go through this tutorial:

* [Step by Step: Getting Started Creating a new Project](VFPS://Topic/_0NB1AL6FM)

This walk though demonstrates some of the core functionality of Web Connection and walks you through a few ways you can generate output and capture input in your applications.

### In case of problems
If something goes wrong during installation and your server won't work you can look at the [Setup Troubleshooting](vfps://Topic/Setup%20troubleshooting) topic, which describes a number of problem scenarios and attempts to take you through them. If these don't work for you you should post a message on the message board at <a href="http://www.west-wind.com/wwThreads/" target="top">http://www.west-wind.com/wwThreads/</a>.

### Checking out the demos
This step serves two purposes: It makes sure that the installation works and lets you see Web Connection in action with demos that you can look at and code you can step through if you choose. Make sure you run the Web Connection server (`DO wcDemoMain.prg`). You Should be able to navigate to <a href="http://localhost/wconnect/" target="top">http://localhost/wconnect/</a> to get started.

Most of the examples on the demo pages have links to show you the source code required to create that request by clicking on the `Show Code` link on the bottom of the page. At this time you can also start poking around in the code, making a few adjustments, maybe creating a new method and stepping through the code to get an idea what's involved in writing code for Web functionality.

### Go through the Step By Step Guides in the User Guide
The user guide contains several getting started guides for Web Connection that let you see how Web Connection works. These are walk-throughs that show you common operations and the workflow to create Web requests with Web Connection.