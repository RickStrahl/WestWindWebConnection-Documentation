In this step you can set up a virtual directory and scriptmap for the new sub application you're about to create.

![](/images/ManagementConsole/NewProc2.png)


### Process Class Type
As with full new projects you can choose to create either a **Standard Process class** for Html style projects (using `wwProcess`), or create a **REST Service API Process class** (using `wwRestProcess`). 
  
The latter works with JSON Input and produces JSON output using RPC style transactional class methods, while the former uses HTML output semantics using Web Connections Html output frameworks (raw output, MVC templates, Web Control Framework etc.)

### Creating a script map
You can and should also configure a new scriptmap that maps to this sub-application.

This allows for script based request routing where the extension (.tp for for TProcess) is always routed to the new class and the page name (Helloworld.tp goes to TProcess::HelloWorld). The default wwProcess handler makes this type of request routing automatic.

Specify the name of the script map. This is a file extension so keep it to 2-4 characters and don't specify a leading `.`.