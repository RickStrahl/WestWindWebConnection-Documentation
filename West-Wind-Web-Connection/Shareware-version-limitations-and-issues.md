<small>not released yet</small>

Web Connection is available in a Shareware version that allows you to check out Web Connection and build and test your applications locally. It's not suitable for deploying running applications and requires that you run your Web Connection applications from inside of the Visual FoxPro IDE.

## Shareware Version Differences
The shareware version provides most of the features of the full version but there are a number of differences.

### No Source Code
The shareware version provides the Web Connection libraries as a `wconnect.app` file that wraps all the libraries. This means you can't directly look at or modify the  source code of the base libraries. It also means **you can't compile your application into a deployable EXE file**. 

As a result you can't:

* Create a Self-Contained Project 
* Compile your Web Connection Application into an EXE
* Can't compile the application into a COM object (only run in file mode)
* The New Project Wizard shows a message that no Project was created

### Shareware Notification on Startup and in 30 minute Intervals
The Shareware version will show a modal startup dialog with Shareware information. You have to click through to start the 
server. 

Also the same dialog will pop up after 30 minutes of continuous operation. Essentially this means you can't use the 
Shareware Version to go live on a server as the dialog will stop the server temporarily.

### Some of the Management Features won't work
Due to the inability to create an EXE from the shareware version there are a number of administration features that won't work in the shareware version:

* No project file is created when creating a new 'project'
* COM Mode doesn't work - you have to run in File mode
* Remote Server unloading doesn't work (requires EXE)
* Application hotswapping doesn't work
* Build processing (build.ps1) will miss files in shareware mode

All of these features are considered 'deployment' related features and so are not supported in the shareware version.

Again, this is not critical when you are building your application during development, which works fine in shareware mode, but when you are ready to deploy the application you'll want to upgrade to a <a href="https://store.west-wind.com/product/wconnect70" target="top">full Web Connection license</a>.

### The Goal of the Shareware Version
The goal of the Shareware version is to give you a fully functioning set of Web Connection features that you can work with on your development setup to check out features and start building your application so you can get a feel for what Web Connection development is like. For development the above limitations aren't a hindrance as you make changes and restart the server frequently anyway.

For deployed application you will need a full copy of Web Connection.

<a href="https://store.west-wind.com/product/wconnect70" target="top">Purchase a Web Connection License</a>