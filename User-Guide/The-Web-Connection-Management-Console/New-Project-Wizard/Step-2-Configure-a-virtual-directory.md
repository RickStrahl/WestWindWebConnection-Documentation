This step deals with configuring a virtual directory and script map for your new Web application.

![](/images/ManagementConsole/NewProj2.png)

### Virtual Directory and Path
A virtual directory is an IIS mapping to a folder that maps a physical operating system path to a 'virtual' path in IIS. You can also leave the virtual path blank to designate you want to configure the root Web site. The Project path is the physical path on disk to the folder, while the virtual is the path that is created in IIS and that IIS will use to reference that path. For the example above the URL represented is `http://localhost/WebDemo` for example.

Virtuals are essentially path mappings that map an IIS path (WebDemo here) or a root folder to a physical location on disk.

### Script Map
A script map is a file extension that is used for mapping requests to a particular wwProcess class. wwProcess classes are 'routed' based on the file extension. In the example the **.wp** extension is routed to the **WebProcess** wwProcess class you set up in step one. Once configured you are then able to access a URL like **HelloWorld.wp** which then maps to the **WebProcess::HelloWorld** method in your custom wwProcess subclass.