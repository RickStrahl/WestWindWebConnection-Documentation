The communication layer between the .NET (or ISAPI) Connector and the Web Connection FoxPro server depends on which messaging mechanism is used. The data is passed back and forth using strings, but the data may be either a physical string (in COM messaging), message files (file based messaging).

Both modes **support simultaneous request processing** by managing multiple instances of your FoxPro server application to work around the limitations of Visual FoxPro's single threaded environment.

### File Based Messaging
This mechanism is the default mechanism and is used primarily **for development and debugging** applications. It uses semaphore message files to pass information between the Web Server and your FoxPro Web Connection application.

![](IMAGES/HOWITWORKS/FILEBASED.GIF)

In file based operation the Web Connector sends a message file that is picked up by the Web Connection FoxPro server application. The server packages the incoming request data into a `Request` object that the application uses to process that request. The HTTP result is then returned to the server as an output file. Inside of the application framework and user code can use the `Response` object to write output into the HTTP output streamcom which writes to a file.

While this mechanism exists primarily for debugging, you can also run this mode as a standalone EXE. File based operation is easy to set up and work with and requires no configuration.

Because of the files created there's some overhead involved, which is critical only when the site running it becomes very busy where the number of message files can cause directory congestion.

You can run multiple file based instances to allow for simultaneous request processing.

### COM Messaging
COM messaging uses your Web Connection application as an EXE COM Server for communication between the Web Server and your FoxPro Web Connection application. It uses a plain parameter passing using encoded strings and returns values as a binary stream created from string output to pass information between the two.

![](IMAGES/HOWITWORKS/COMBASED.GIF)

The FoxPro server is compiled into a COM object and receives the request input as a parameter of one of the COM methods. The HTTP result is returned as a string return value of the method call. 

COM messaging performs better than file based operation, but is more difficult to set up and configure. Additional advantages include usage of the Web Connector pool manager for scalability, remote server management, auto server restart.

You can configure a pool of COM instances (up to 32) to allow for simultaneous request processing. The Pool is managed as part of the ISAPI extension, which starts the servers and manages their lifetime. The big benefits of COM are better performance, the ability to automatically start servers on the first hit and to automatically recover from any hangs or crashes inside of your FoxPro code.

### It's all transparent
The Visual FoxPro Web Connection framework handles all these messaging mechanisms transparently, automatically adjusting inputs and outputs to the appropriate types. Your application user code does not need to differentiate between these operational modes and should work without any modifications in either mechanism. -