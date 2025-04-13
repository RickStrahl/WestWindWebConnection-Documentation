This step deals with configuring a virtual directory or root web site folder. It configures the base settings and permissions required to run a Web Connection application out of the specified folder.

![](images%2Fmanagementconsole%2Fconfigure1.png)

### Pick your Web Server
This step is always required, so make sure you select the correct Web Server from the list of servers.

### Creating the Virutal Directory
The next option is to create a virtual directory where your HTML files live. 

Specify a folder and a name of the virtual directory you want to create or update settings for. 

**Root Web Site Configuration**  
If you are configuring a root Web site (ie. no virtual directory) leave the *Virtual* field blank. Configuration then occurs on the root directory.

### What happens

This step creates the virtual directory if it doesn't exist, set permissions on the folder, create or update the folder to use the West Wind Web Connection Application Pool.

It's quite common that you will develop your application in a virtual folder such as /WebDemo, but run your live site on a root Web. When this is the case leave the virtual name blank and point the path at the root of the site.

<div class="notebox">**Note:**  
The Web Site to configure must already exist before running this Wizard. Any folders you like to configure must also exist already.
</div>

<hr>
Goto [Step 2](vfps://Topic/Step%202%20-%20Synch%20ISAPI%20DLL%20and%20the%20Web%20Connection%20Server)