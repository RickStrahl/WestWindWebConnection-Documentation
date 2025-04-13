In this step you can set up a virtual directory and scriptmap for the new sub application you're about to create.

![](IMAGES%5CMANAGEMENTCONSOLE%5CNEWPROC2.GIF)

### Creating the Virutal Directorty
In order to create a virtual directory you have to specify the virtual directory as well as the path that this directory is to refer to. Virtual directories in general are used to provide logical access to a physical path on the hard disk - you can map a non-Web path (like d:\somepath) logically into the Web so that it can be accessed as http://localhost/myvirtual/. Virtuals also serve as an application entity - Cookies, Authentication and rights always apply from a given virtual downward, so in general it's a good idea to create virtuals for an application's base path (if it's not running at the root of the Web server).

### Creating a script map
The Wizard also allows you to configure a scriptmap that maps to this sub-application. This allows for script based request routing where the extension (.wpfor WebProcess) is always routed to the new class and the page name (Helloworld.wp goes to WebProcess::HelloWorld). The default wwProcess handler makes this type of request routing automatic.

Specify the name of the script map (this is a file extension so keep it to 2-4 characters - no period). Then point it to the Web Connection dll (wc.dll) of the application that this process class will be hooked to.