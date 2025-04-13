Web Connection supports and should be run in production with multiple server instances to allow for multiple requests to be processed simultaneously. Both File and COM modes support multiple server instances.

Multiple instances are necessary so requests can process at the same time. A single instance can only process one request at a time, so if two long requests come it at the same time one has to wait. Multiple instances solve this problem.

You can configure Web Connection to use a specific number of instances to run simultaneously. Web Connection can start a configured number of server instances for you and in the case of COM can monitor those instances and if they crash restart them.

## Starting Multiple Instances
Depending on how you run Web Connection there are a number of ways that you can start new instances. For file mode in particular you have several options, while COM mode can only be launched through the Web interface or automatically.

### File Mode - Run multiple Exes
For File Mode to run multiple instances you can simply start up multiple instances from Explorer or the Windows terminal.

You can also start new instances from the .NET Module Admin Console.

![](//images/ManagementConsole/StartFileBasedInstancesFromAdminConsole.png)

The `web.config` also includes an `AutoStartServers` flag which applies only to file mode. When `true` it automatically starts servers if they are not already running when the application is first hit. 

### COM Mode
COM Mode can only be 'started' through configuration which is done by switching into COM mode and specifying a server count for the number of instances to fire up.

![](//images/ManagementConsole/StartComBasedServersFromAdminConsole.png)

COM instances are autoamtically started when a hit comes in or you can explicitly launch the servers using the 'load servers' button in the admin form.

## How Many Instances Should I Run?
How many instances you run depends on the kind of application you are running and also on the hardware that you are running on.

The base guideline is this:

> For optimal CPU usage **use 2 instances per CPU physical or virtual CPU Core**. At minimum I recommend 2 total instances to allow for slow processing of 1 instance.

So if you have a 4 core computer you can easily run 8 instances of Web Connection simultaneous.

For most small applications, you probably don't need that many instances to run an effective application. However, I highly recommend running at least 2 instances for anything but the simplest, non-busy applications. Having at least 2 instances will ensure that if you have a slow request you have at least something to fall back to. 

Personally I tend to run most of my applications with 2 instances - for example the message board and Weblog both run with 2 instances. While neither is very busy both have a few slow requests (searching in both cases) and having the extra instances frees up the application during searches.

### Figuring out best load factor
If you have a busy application and you want to find the right amount of instances that you can load up, there's an good process you can use to check for load.

> Note the following only works for COM mode

* Set the Web Connection Server into Load Based  Server Loading
* Set the server count near the high end you expect
* Run a load test (you can quickly set one up with [WebSurge](https://websurge.west-wind.com))
* Go to the Web Connection Handler Page
* Watch the # of Hits
* Where the # trails off or goes to 0 is the sweet spot

![](//images/ManagementConsole/LoadInstancesSweetSpot.png)

Notice that after 8 instances the request count drastically drops off. This likely is because I didn't actually generate enough load to keep the server busy but this is what the simulated load emulates.

This doesn't automatically mean that that spot is where you should run your servers. If you load too much traffic and you are swamping the CPU with too much traffic, running at the max load where it trails off may very well mean that you are maxxed out on CPU resources. If that's the case, try making the server count number lower. 

Essentially using this approach you're looking where your server is topping out or the requests that are processing are not actually enough to overload your server instances. You need to look at this number in combination with the CPU load of the server - if the server is maxing out CPU, then you have to try to dial down the number of instances and hope that you still handle traffic. In some cases you may simply have too much traffic for a machine to handle in which case you have to think about load balancing across machines.


> #### IIS on Windows Client OS is limited to 10 simultaneous connections 
> If you're testing on a Desktop Windows Client Install, please note that IIS is limited to 10 simultaneous connections. This means that on a local machine you're likely to see a drop to see a bottleneck at 10 instances for high volumne requests. For realistic server load testing for rapid fire requests you may have to check load on a server.

### Scenarios
There are a number of different scenarios for applications and how you can handle instance count.

* **Busy High CPU Application**  
Everything I've talked about above - 2 Instances per CPU specifically - applies mostly to CPU intensive applications. Basically if you're application is CPU bound it'll use as much resources as are available because FoxPro is not very good at sharing. You do not want to run 100 instances simultaneously chewing on CPU intensive operations as it will drag the CPU to 100% and leave it maxed out. Less can be more when it comes to scalability of FoxPro processing.

* **IO Heavy Application**  
If your application uses a remote SQL Service on a different machine, a lot of your heavy duty processing is done on another machine, while your application sits and waits on the remote server call to return. This doesn't tie up the CPU nearly as much as local data processing and it means you can run more instances locally without critical overhead.

* **Low Volumne Applications**  
If you have an application that isn't very busy I'd recommend running with 2 instances. Even if the application is very lowly loaded having that second instance tremendously helps responsiveness of the application.

* **Low Volume Applications with Long Requests**
If you have an application that's not very busy but has several long running requests you might want to run more instances than you normally would. Although the same CPU load issues mentinoned above apply, you'll want to have the extra instances to avoid locking up your requests, due to all servers being busy. 

* **Use Single COM Mode for Long Requests**  
If you have a few very long requests in your applications you can use Single COM Mode to run these requests on COM servers that are started up to run that single task. Rather than using one of the server instances from the COM thread pool, a brand new instance is started in addition to the ones in the pool and run independently. The server is the same server, but it's run as a single request and then shut down. For more info see, [Running long Requests in Single COM Mode](VFPS://Topic/_02J0QGVSS).

## Load Related Configuration Values
There are a number of settings related to server loading. The most critical one to instance counts is the `ServerCount` property which is also set on the Module Admin form.

```xml
<add key="ServerCount" value="12" />

<!-- COM Server Settings -->
<add key="ComServerProgId" value="wcdemo.wcdemoServer" />
<add key="ComServerLoadingMode" value="LoadBased" />

<!-- File Server Settings -->
<add key="AutoStartServers" value="True" />
<add key="ExeFile" value="~\..\wcdemo.exe" />
```    

For file based servers `AutoStartServers` determines whether servers are automatically started if they are not already running. The `ExeFile` points at the EXE that is launched which should be your Web Connection server EXE.

For COM Servers you provide the COM Server ProgId to identify the COM server that should be loaded.

The ServerCount is applied to either File or COM based messaging.