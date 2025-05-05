The first step in this walk through is to set up a new Web Connection project. To do so let's start with the Management Console and the [New Project Wizard](vfps://Topic/_S7R0OXD9T). If you're unfamiliar with Web Connection you might want to quickly review how the NewProcess Wizard works at the link above. Here's the short version. 

Let's create a project with the following characteristics:

![](/images/WebControls/ProjectWizard_1.png)

Set up a Project called DevDemo (which will have DevDemoMain.prg) as it's startup and a process named DevProcess. We won't actually use this process class in this demo, but it's a good idea to set up a new project anyway in case you decide later you want to override the default page handling behavior.

![](/images/WebControls/ProjectWizard_2.png)

Next create a virtual directory. Notice the checkbox for Web Control Support. When checked this copies some additional files into your Web Directory. A couple of files to configure Visual Studio specifically. Copy a separate copy of wc.dll into this directory so this app is free standing.

![](/images/WebControls/ProjectWizard_3.png)

Finally configure a scriptmap for the new Process class. In this case I'll choose .DP (for DevProcess), which will be the extension used for the demos. Note that you can also use the generic .WCSX extension, but using a custom script map allows you to associate a specific Process class with your project, so you can override Application wide settings. Using .WCSX is easier, but using a custom extension gives you more control.

### Starting up
Once the project completes building you should see a browser window pop up. In Visual FoxPro you will see a project window with a new project created. Note, if you are running the shareware version no project is created since there's no source code to create a project with. You can still run this walk through though simply by using the PRG files.

Start up your DevDemo server with the following code from the VFP command window:

```foxpro
CLEAR ALL
CLOSE ALL
DO DevDemoMain.prg
```

A server window should pop up at this time waiting for a request to start up.

Switch over into the browser's window and either use the page it automatically loaded for you or type:

<a href="http://localhost/devDemo/" target="top">http://localhost/devDemo/</a>

into the browser's Address bar. You should now be able to click the first and second link and get a very short Hello World message that confirms that the server is up and running and ready to take requests. Make sure that both links work before moving on.

### Configure the Application
We need to make a couple of small configuration changes to your new Web Connection Server Application.

First **create a DevDemo directory** below the Web Connection root, which we'll use to store our Page classes that contain the CodeBehind for the visual pages we'll create.

Next let's make one small change to the generated main program file to allow the application to find our data files. I'm going to use the wwDevRegistry sample data in the wwDevRegistry directory underneath the Web Connection root directory. 

To find the data we'll need to SET the FoxPro path. To this we'll add the path to the server's start up code in the OnLoad() method:
* Open DevDemoMain.prg
* Find the OnLoad() method
* Add this code to the bottom of the method at the marker:
```foxpro
* ** Add any data paths - SET DEFAULT has already occurred so this is safe!
DO PATH WITH THIS.cAppStartPath + "wwDevRegistry\"
DO PATH WITH THIS.cAppStartPath + "devDemo\"

* ** Add any SET CLASSLIB or SET PROCEDURE code here
SET CLASSLIB TO wwDevRegistry ADDITIVE
```

Now the server is ready for creating our samples.

**Next Step:** [Opening the Project in Visual Studio](vfps://Topic/_1LX0R1R7U)