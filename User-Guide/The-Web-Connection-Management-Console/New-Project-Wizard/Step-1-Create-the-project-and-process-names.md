In this step you specify the name of the project and the name of your main process class that will handle requests. You also pick your Web Server to install on. 

![](IMAGES\MANAGEMENTCONSOLE\NEWPROJ1.png)

In Web Connection terms the first two options  create a main program file - a wwServer subclass - and a process class - a wwProcess subclass.

* **Main Program File**  
The main program file is the Web Connection entry point that contains a wwServer subclass. This class is used to handle server configuration and acts as the entry point for each incoming Web request. The main program file is `WebDemoMain.prg`.

* **Process Class**   
The Process class created is the 'request' handler implementation of your application. Just about all the code you write to handle Web requests will be handled by this class, with each method representing an HTTP endpoint.

### Web Server Configuration
On the bottom of the dialog you get to pick the Web Server you want to install on. In most cases you should choose the default option which is IIS with the .NET Handler which is the most efficient way to go. You can also choose IIS Express for development if you don't have IIS installed or you don't have permissions to run IIS.