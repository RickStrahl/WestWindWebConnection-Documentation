The next step is to configure the Web Connection installation and set up the samples so that you can run the sample Web Connection server. To do this you specify the local folder where the samples and Web virtual directory are installed.

![](/images/managementconsole/setup2.png)

### Web Files Path
This path points at the local installation folder for the HTML files and templates, CSS, JavaScript and other support resources. For the demo this folder is not configurable as it points of the pre-created `.\Web` folder.

### Virtual Path for IIS
If you chose IIS as the Web Server you can choose to use a virtual directory to host your Web application which by default is `wconnect`. This creates a virtual below the Web root for a URL like this:

https://localhost/wconnect

For the demo you shouldn't change this value.

> For non IIS installations ie

Web folders are described by a disk location and a virtual path. The virtual path is a Web server relative path that you see on a URL. Effectively a virtual path is a mapping of a folder name in IIS to a physical folder name on disk, which corresponds to the two textboxes in the form above. 

Virtual Paths can either be a folder name or empty in which case the root folder on the Web site is used.

### What does it do?
The Wizard creates an IIS virtual directory for the folder you specify and configures the configuration files for both the Web virtual and the main application so they can communicate. 

The Wizard also adds the appropriate permissions for the system IUSR account and blocks access to the Admin folder by removing the IUSR account. 

---

[Step 3 - Finish and Test the Server](VFPS://Topic/_S8H1EZ705)