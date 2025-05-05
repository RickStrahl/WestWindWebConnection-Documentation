First let's add a new process class to the existing WebDemo project. If you didn't do the first <a href="Step by Step guide" target="top">Step by Step guide</a> go through Step 1 and 2 of that guide first to create the new project, then continue on with these steps.

Next start up the Web Connection Console in the Web Connection startup folder:

```foxpro
CD c:\webconnection 
DO Console
```

Then click on the *Create New Process Class* link. Fill out the Wizard as follows:

![](/images/stepbystep/BusStep1.png)

First select your main program file which is `WebDemoMain.prg` (or whatever your main project's name is). This is used to attach another process class to the main server.

Next enter a name for your new Process class. We're working with customers so I'll use `CustomerProcess` which will be the name of the PRG generated. The methods you create become HTTP endpoints you can access via your browser or other HTTP client.

Next, we want to add a new virtual directory and script map (.cd) for this sub application.

![](/images/stepbystep/busStep2.png)

Click Finish to let the Wizard create the class, the virtual directory and copy the files into it.

Let's make sure to test the new applet by:

* `CD c:\WebConnectionProjects\WebDemo\Deploy`
* Start the WebDemo server with `DO WebDemoMain`
* Browse to <a href="http://localhost/CustomerDemo/default.htm" target="top">http://localhost/CustomerDemo/default.htm</a>

You should see the demo page. Click on the <a href="http://localhost/devprocess/helloworld.wp" target="top">Helloworld Test Page</a> link which brings up the default test page for this application.

If all is working you should get a simple page response that echoes back the time.