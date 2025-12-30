In this step you can set up a scriptmap for the new sub-application  you're about to create with your new Process class.

![](/images/ManagementConsole/NewProc2.png)

### Process Class Type
As with full new projects you can choose to create either a **Standard Process class** for Html style projects (using `wwProcess`), or a **REST Service API Process class** (using `wwRestProcess`). 
  
The latter works with JSON Input and produces JSON output using RPC style transactional class methods, while the former uses standard Web Connection HTML output using Web Connections Html output frameworks (raw output, MVC templates, custom Html rendering etc.)

### Creating a script map
You can and should also configure a new scriptmap that maps to this sub-application. The scriptmap gets attached to the current Web site or virtual directory so that requests are routed to your new Process class. 

This is a file extension so keep it to 2-5 characters for url brevity (but you can make it longer). 

The script map maps the extension specified (ie. `.tp` in the screen shot) to your process class, where `method.tp` then becomes an endpoint that maps to the method in the class. The behavior here is exactly the same as in your main process class except it uses the new extension for mapping.