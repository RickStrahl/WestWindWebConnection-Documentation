A topic that comes up frequently is that people want to 'copy' a COM Server to run as a separate application.

This is easy for file based servers, which you can just copy and run in a new location. However, for COM Servers - due to their central registration in the Windows Registry that points to a physical location on disk - this process is not so easy and you should think long and hard whether you want to create a copy especially if the copy has to be kept up to date. 

## Creating a Copy of a COM Server
In order, 'copy' a COM server you have to essentially create a new project and rebuild the server.

The new server has to:

* Have a new ProgId (ie. `MyApp.MyAppServer2`) different from the original server
* Have a new set of ClassIds which are tied to a FoxPro PJX project file

There are two ways to do this:

* Create a new project and re-import all files for the project *most reliable*
* Copy the old project and change the ProgId and ClassIds in the project

> It's vitally important when you copy your server that **both the ProgId and ClassId are different** from the original project!

### Creating a new Project
The more reliable way of the two is to create a brand new project and rebuild the project from scratch. If you use Web Connection's recommended approach of importing all dependencies as part of the `OnLoad()` handling this process should be quick and easy as all dependencies are auto-discovered and pulled into the project. You simply add your `MyAppMain.prg`.

#### Step 1 - Rename the Server Class
You need to generate a new ProgId and the only way you can do this is by renaming the FoxPro server class before you rebuild the project with the new ClassId. The ProgId is the EXE file plus the name of the class. (ie. `MyApp.MyAppServer`). For the copied project you probably want to change the server class to `MyAppServer2`.

This produces a new ProgId

#### New ClassId gets generated when you Build your Project for COM the first time
FoxPro will generate a ClassId if your project contains any COM servers. It'll write one or more of these ClassIds into the `Reserved2` field of the `PJX` record of the project file.

There's nothing to do here if you created a new project, the new classIDs are automatically generated. 

> Just make sure that the ProgId is different than the original project **first** before building. You don't want to have the old project associated with the new ClassId - that'll create a mess in the Registry.

### Copying a Project
If you insist on copying an existing project the process is a little more involved. As with the new project we suggest you copy your entire project - source files and all to a new location.

#### Step 1 - Rename the Server Class
You need to generate a new ProgId and the only way you can do this is by renaming the FoxPro server class before you rebuild the project with the new ClassId. The ProgId is the EXE file plus the name of the class. (ie. `MyApp.MyAppServer`). For the copied project you probably want to change the server class to `MyAppServer2`.

#### Step 2 - Remove existing ClassIds from the PJX
Next you need to remove the classIds from the project file:

* Close the project
* `USE YourProject.pjx`
* Find the `YourProject.PJX` record in the `Name` field *(usually first record)*
* Clear out the `Reserved2` field
* `USE` && close the project file
* Make sure the ProgId for the project is different than the original (ie. `MyApp.MyAppServer`)

Then rebuild your project - it should generate a new `Reserved2` value with a new ClassId.

> Note it's important that **both** the ProgId and ClassIds are different. If either of them are the same as another project you'll have potential corruption.

#### Rebuild your project
Next rebuild your project using:

```foxpro
BUILD EXE MyApp.MyAppServer2 from MyAppServer
```

### Checking out your new server
Regardless of which approach you took you should now be able to instantiate both the old and new servers via COM:


```foxpro
o = CREATEOBJECT("MyApp.MyAppServer")   && old server
*** You can point this at your own process class (instead of wwMaint) and maybe echo a path back
*** to show which server is used in which location
? o.ProcessHit("query_string=wwMaint~FastHit")


o2 = CREATEOBJECT("MyApp.MyServer2")   && new server
? o.ProcessHit("query_string=wwMaint~FastHit")
```