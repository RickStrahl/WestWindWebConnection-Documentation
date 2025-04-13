`wwServer` is your FoxPro application's entry point - when a Web request comes in `wwServer` is the first point of contct with FoxPro code that starts of the request processing sequence.

wwServer is:

* The server application's entry point
* A 'server' waiting for requests
* Accepts COM or File based requests
* A router that routes request to the appropriate `wwProcess` class

> #### @icon-info-circle Multiple wwServer Instances 
> You can run multiple wwServer instances simultaneously in separate processes. In File based operation multiple EXEs can be started, in COM the COM instance pool can be set to load multiple instances.

### File based or COM based use the same Server
The server supports both **file based** and **COM based** through the same `wwServer` implementation. In file based messaging the Web server request data is sent via message files that the server picks up and processes and then writes out results to files which are picked up by a timer in the server form. In COM based messaging the Web Server directly instantiates and calls the `wwServer` instance and Web Connection returns a string based HTTP response as a result.

For your code the mode should have no direct impact other than perhaps permissions and the execution environment in terms of location. Web Connection always sets the server's execution path to the EXE's startup folder.

### Startup Sequence and Server Configuration
It's useful to understand how the startup order of events works in Web Connection. 

#### OnInit
The initial server initialization that fires off the server class' `Init()` operation. Fires once when the server class is instantiated, before Web Connection configures server instance. 

This method should not have any code beyond what's auto-generated and is required to run the internal configuration of Web Connection during startup. The default implementation merely points at the startup configuration file (`<myApp>.ini`) and rarely should have anything else in it.

All **application configuration code** should go into `OnLoad()`.
*This method only fires once when the server starts up*

#### OnInitialized
Still part of the server's `Init()` sequence during server startup, this method is fired immediately after the Web Connection server has initialized itself and has read configuration values from the application configuration.
*This method only fires once when the server starts up*

#### Onload
This method is called to initialize the server with your **application configuration** logic. This method can be used to:

* Set the FoxPro Environment for your application
* Set paths to find your application dependencies
* Load Classes and Class library dependencies
* Open persistent SQL connections
* Set up any global objects or cache values

Use this method to **configure your application's environment** to make sure your application can run. Note that this method fires only **after the first request is hitting your server** which is changed behavior from older (pre-7.x) versions of Web Connection.

*This method only fires once, when the first request fires against the server*

#### Process
Fired on every incoming request from the Web Server. This generated method contains the routing logic that routes request to the appropriate wwProcess class that house your Web processing logic. This class contains logic that parses the URL and based on the URL parameters or script extension route the request to the wwProcess class. Typically you don't have to change code in this method unless you manually change your scriptmaps.

*This method is fired for every request*

#### ProcessHit
`Process()` is the application level entry point where application code is written in your server instance, but `ProcessHit()` is the low level entry point for the application. This is the method that is initially called by COM server calls, or from the timer that fires when files are found in File based processing. 

This is an internal method and generally you don't ever override it. 

When requests come in, `ProcessHit()` is the initial entry point for Web Connection. This method gets passed the raw request data from the Web Server. It takes this data and parses it into a `wwRequest` object that wraps this data and makes it accessible to your application. The method also handles logging, timing, bringing up the UI and last resort error handling, before passing off a valid request to be processed in the `Process()` from the last section.


Think of `OnInit()` and `OnLoad()` as your one time configuration methods. `Process()` is a static handler for every request that comes into Web Connection. The only method you typically change and add code to is `OnLoad()` where you configure your application startup environment.

> #### @icon-info-circle Use OnLoad() for Configuration
> The main Web Connection start program contains a small bit of non-class FoxPro code that is used to bootstrap the Web Connection server when running in file mode. It may be tempting to put configuration code there, but **you should not put configuration code into the startup code**. When running under COM that code is not fired, so if you switch between COM and File based you won't get the same behavior.
>
> Always use `.OnLoad()` to configure you Web Connection application to ensure that the startup code always runs.

### Implementation
When you create a subclass of wwServer you should implement the following methods:

* OnInit()
* OnLoad()

The code looks something like what's outlined below - this should change very little with a few adjustments for your server environment.