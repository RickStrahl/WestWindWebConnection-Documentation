Let's start by creating a new **wwRESTProcess class** for our Customer business object. The goal is to create a simple CRUD service that can handle queries and allows adding and updating of customer records.

The first step is to create a new service. We'll use the **New Process Class Wizard** on the Web Connection Console to add the service class to the existing CustomerDemo example we built in the [MVC/Business object Tutorial](VFPS://Topic/_0I102WSAI). If you prefer you can also create a new project using the **New Project Wizard** instead.

> **Note**: If you're creating a new project, make sure you run `console.exe` **from within the Visual FoxPro IDE** to create a new project! If you don't, FoxPro can't create a new project file and will only create the source files. 

Using the **New Process Wizard** do this:

* `cd c:\wconnect`  && or where ever your Web Connection installation lives
* `DO CONSOLE`
* Choose **Create New Process Class** (shown here)
* If you're creating a new project choose **Create New Project** (not shown)

Start by specifying the base project the new process class will be connected to and the name of the new Process class you wish to add.

![](/images/stepbystep/NewRestProcessClass1.png)

Next we need to configure the new process class and associate it with a virtual directory:

![](/images/stepbystep/NewRestProcessClass2.png)

In this case I'm going to choose an existing virtual directory of the previous customer demo created in the MVC with Business Objects tutorial. So I choose that existing path in `.\customerdemo` and `CustomerDemo` as the virtual. Essentially this recreates the virtual settings and adds the new scriptmap(s) in that virtual.

> ### @icon-warning Overwrite Warning
> When you use the **Add Process Class Wizard** and install to an existing virtual directory you are overwriting existing templates and sample pages. If you don't want to override any existing templates, it's best to generate to a new virtual and physical directory and then simply add the scriptmap to your existing application's web.config file, and change the application paths in your `yourApp.ini` file.

Click **Next** or **Finish** to complete the Wizard.