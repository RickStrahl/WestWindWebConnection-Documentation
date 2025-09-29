When you run Web Connection locally for development, you typically run a single instance, most likely using your FoxPro IDE where you can run and debug your code interactively just as any program. When an application is deployed to a live server for production, the needs are very different as you need to account for performance, scalability, reliability and administration.

When you develop your application locally, you see none of the production features typically, so in this topic I'll give an overview of the differences and how Web Connection works in production.

## Different Goals for Development and Production
There's necessarily different focus for development and production environments.

### Development Priorities

* Ease of use
* Able to quickly start and stop
* Able to debug code on request
* Limited rights development (no IIS potentially)

**Usage** 

* Local Development Web Server (IIS Express, Web Connection Web Server)
* Web Connection running in File Mode
* FoxPro IDE
* Running PRG or EXE interactive
* Requests are debuggable in FoxPro IDE
* Single instance

> This isn't to say you can't set up a local machine like a server and run IIS and COM with multiple instance, but the above is the typical and recommended local developer setup.

### Production Priorities

* Best Performance
* Handle multiple simultaneous Requests
* Ability to recover from Failures
* Ability to remotely administer Server

**Usage**

* Running in IIS
* Typically running COM Mode
* Multiple Web Connection FoxPro Server Instances
* Error Recovery
* Hot Swapping of new Binaries
* Administration access

Note that you can also run a server with multiple File based servers, but there should be a very good reason to do so. The more efficient and more reliable way to run and manage server instances is in COM mode with multiple instances configured for your server.

## Web Connection Servers in Production
So as you can see the priorities for production are very different from development, with production focused on best performance, reliability and maintenance rather than developer productivity.

### Scalability, Reliability and Performance with FoxPro
FoxPro is a very old development environment and it was never meant to run in a multi-threaded environment that Web applications require. What this means is that **a single FoxPro application instance can only process exactly 1 request at a time**. When you're running FoxPro in development by firing up the Web Connection server from the FoxPro IDE, you can effectively only process one request at a time.

In order to run multiple requests **simultaneously** you need to run multiple FoxPro/Web Connection instances. You can literally do this manually: Start up two instances of the FoxPro IDE and run your server, or start up a compile EXE twice. 

While that works, it's inefficient and a manual process that's unreliable. However, Web Connection has automated processes that can manage the starting and stopping of instances for you.

The bottom line for this is that **in production you typically end up running more than a single Web Connection server instance**.

Web Connection makes this possible by isolating the FoxPro processes into distinct instances that are served off a custom **Thread Pool** that manages FoxPro instance activation.

### Server Administration
When you run your server in production you are likely going to run it on the IIS Windows Web server. Once you've deployed the application - copied the EXE and all its dependencies, all the HTML resources etc. the server is ready to run on the server.

<small>

> See more for: [Deploying Web Connection Applications](dm-topic://_0j40wjbwg) 
</small>

Assuming you have published your Web Connection application, you can adminster the Web Connection server through the `Administration.wc` admin page:

![Web Connection Server Admin](/images/WebConnectionServerAdmin.png)

The page lets you see the active state of the server and running instances as well as letting you configure the processing type and how many instances to run.

### COM or File Based
For production servers COM based servers are the recommendation because they provide the greatest amount of remote administrative functionality as well as the best performance. 

However, in some scenarios running File Based servers might be required:

* Multiple application using the same binaries (not supported via COM)
* FoxPro Servers running on different machines from Web Server (including Linux)
* Unresolvable Machine Permission issues (in some corp environments)

File based operation cannot be automated in the same way as COM based services - in particular Web Connection cannot detect crashes in servers and automatically restart servers in file mode. However, most other features do work. Note that File based processing in very busy sites can be problematic due to the semaphore files that are used for the 'file based' messaging. 

> For 99% of installations a COM based setup is the way to go and unless there are specific needs to use file based processing it should be avoided.

## Number of Instances
The #1 question that ultimately gets asked for server execution is: **How many instances should I run?** 

As mentioned FoxPro is single threaded so 1 instance maps to 1 simultaneously operating instance. If you 4 instances running 4 dynamic request can process simultaneously. 


### Baseline Recommendation
The base line recommendation for maximum amount of COM server instances is:

* Max 2 Web Connection Instances per CPU/Core/CPU Thread

This is based on the fact that FoxPro is single threaded and tries to monopolize the CPU resources it uses. While it's processing FoxPro code (not waiting on IO like SQL though) it hogs the CPU it's running on. While modern OS's and CPUs internally don't allow to completely take over a CPU, a single app like FoxPro can still put a lot of strain on that CPU. It's because of this CPU segmenting the recommendation is 2 per CPU rather than just one.

The number of instances depends on your application:

* How busy is your application
* How long do requests taken on average

If you have a highly responsive application that processes requests in a few milliseconds, 2 instances can serve literally hundreds of thousands of requests in an hour!

However, if you have an application that processes a few requests that take 20 seconds 10 instances may not be enough! 

If you have no idea my recommendation is this:

* Start with 2 instances
* Run a normal load scearnio
* See if your two instances are getting large numbers of requests
* How often does the last server in the list get hit
* If the number is high compared to the first instance add more instances


What you're shooting for is to balance the number of servers to the load, so that every request gets served either immediately or after a tiny break. Basically you want to see the last server in the list to get hit at a small percentage of the 1st server.

For most typical application that don't have long running requests a few servers is all you need. Most medium load applications do well with 2-4 instances.

One take-away is that **you want to minimize long running requests**. If requests take more than 5 or 10 seconds you should consider offloading them to some external queued process that can process it asynchronously and report back to your application when it's done.

### Minimize Long Running Operations
**Slow processes are a severe limitation for FoxPro** because it ties up one of the precious instances while it's working. Get enough at the same time and you can overrun the pool of available instances. 

Anything running over 5-10 seconds should be considered long running and should be examined for possible offloading in some way.


> #### @icon-lightbulb Single COM Mode to avoid Pool Overload on Slow Requests
> If you have a few well-known slow requests in your application, you can explicitly run them in a special [Single COM Mode](dm-topic://_02j0qgvss) that runs outside of the COM pool by starting an isolated, non-pool COM instance that is shut down after the request has completed.  
>
> This leaves your COM pool running 'fast' requests and leaves the slow but infrequent requests to run independently. Only works in COM mode.

You'll want to avoid long running requests by either use Single Mode Com or by offloading those processes to some sort of asynchronous, queued processing on another machine or at least outside of the Web Connection, where the process is run externally and then calls back when it's complete to be picked up for retrieval or notification.