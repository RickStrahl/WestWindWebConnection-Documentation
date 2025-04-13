Web Connection is capable of running applications that are very high volume and the framework has been run in live applications that serviced in excess of 15 million requests daily on a single dual processor machine. Operation for this kind of load or even bigger loads requires tuning and stress testing to find the best combinations for the number of servers to load.



> #### @icon-clock-o Stress Testing with West Wind Web Surge
> <img src="http://websurge.west-wind.com/images/WebSurgeIcon.png" style="float: left; margin-right: 15px;" /> Need an easy tool to test your Web applications? Check out <a href="http://websurge.west-wind.com/" target="top">West Wind WebSurge</a>, which makes it easy to capture application sessions and play them back either individually or under heavy load. Stress Testing made easy.

The first step for high volume applications should be to tune the application so it is optimized to perform as fast and efficiently as possible. For a number of hints on how to tune your Web application, the Web server, the machine, database operations and code check out:

**<a href="http://www.west-wind.com/presentations/largeweb/" target="top">http://www.west-wind.com/presentations/largeweb/</a>**  

This article describes a number of things to optimize performance.

The next step, which is also described in the above article is tune your application for the hardware it is running under. This process involves stress testing the Web application with a Web Stress tool such as Microsoft's Web Application Stress Tool (WAST). You can find out more about stress testing and how to use this tool at the following URL:

**<a href="http://www.west-wind.com/presentations/webstress/webstress.htm" target="top">http://www.west-wind.com/presentations/webstress/webstress.htm</a>**  

This article describes the process of stress testing as well as general concepts of what it takes to balance machine resources against the running application. The concepts in the article are fairly general, but apply to a Web Connection application as well.

Understand that performance is a relative thing and depends entirely on your application. For example, in simple test scenerios we've run Web Connection with over 350 requests/second, but this is an unrealistic expectation for an application that actually performs something useful. The above is sort of a raw throughput number, which will drastically decrease once you do actual work in your code. So a typical transaction based business application with short (.10 second or less) requests will generally run somewhere around 50-100 requests a second. Your data, business and output generation logic will determine exactly how scalable your application is. Remember that long running requests are the killer of scalability!

For Web Connection applications specifically, the key factor is balancing CPU usage against the number of instance that are run. As a general rule for high performance applications use these settings as a starting point:

* **Run under COM operation**  
COM operation is much more efficient than file based operation under load - for pure request overhead on a loaded server COM is about 2-3 times faster. COM operation also includes many maintenance benefits and supports auto recovery if a server should hang or die.

* **Run two to three instances of Web Connection per processor**  
CPU usage will be affected by how much code is running at any given point in time. Two instances running code simultaneous have to share the machine's resources. Running too many instances and having too much simultaneous code executing will actually result in worse performance. Two instances is good starting point but if you have real data intensive apps that run lots of long requests against local VFP data that maybe too much as well. Multiple instances work best if you have some long requests and more short requests - multiple instances will allow shorter requests to run while longer ones process simultaneously. Multiple long CPU intensive requests running simultaneously will be counter productive and actually slow requests if contending for the same processor.

* **If you use a remote SQL server you can run more instances**  
As a general rule use two instances per processor installed on the machine if the data accessed is VFP data or on  a SQL server that's on the local machine. If the data sits on remote SQL server box the data processing is offloaded to another machine and VFP actually waits idly while the data request is made. This minimizes CPU load on the Web server machine and allows you to run more simultaneous instances of Web Connection. In this scenario adding additional WWWC sessions may not affect CPU usage significantly and thus is much more useful. Careful though: Make sure the SQL backend doesn't become your bottleneck and monitor that as well as the Web app.

* ** Use stress testing to find your break points**  
Don't assume anything - test and make sure. Using WAST you can see what performance you can expect on your site under load. You can find the breaking points and experiment with the number of instances running simultaneously to find the performance sweet spot.

* **Use Load Balancing to scale out to multiple machines**  
Extremely busy sites can outrun a single box and load balancing makes it possible to use multiple Web servers running under a single combined 'virtual' IP address to serve requests. This mechanism is relatively easy to set up and allows scalability to almost any kind of load on the Web server although you may have to watch your data scalability (VFP or SQL for that matter!). Windows 2000 Advanced Server includes the Network Load Balancing Service which provides multi-machine load balancing for IP services including a Web server. You can find out more about Load Balancing at: **<a href="http://www.west-wind.com/presentations/loadbalancing/loadbalancing.htm" target="top">http://www.west-wind.com/presentations/loadbalancing/loadbalancing.htm</a>**.

* **Tune your Web Connection install**  
There are several things you can do to speed up requrest handling in your application:
  * Turn off the Server user interface with the ShowServerForm and ShowStatus switches in the app INI file. This runs the server without a form which improves performance.
  * Turn off logging. If you don't need the logging features explicitly for reporting on usage, turn off logging. You can turn it on as needed if you need to do stress tests or want to check for hack attempts etc. as needed. 
  * Tune IIS and turn off ASP session if you are not using ASP or ASP Sessions.
  * Run IIS in Low Process Isolation for your application. This will improve overall performance drastically for ISAPI request handling (this doesn't apply for IIS 6 or later any longer)

* **Startup for ultra busy sites**  
Some sites have reported occasional problems updating EXE files and getting COM instances loaded due to tremendous startup traffic when the Web server first starts up (backed up requests). The COMLoadLockout switch in wc.ini handles initial load problems by locking out users while COM instances are loading returning a message to the user that the application is busy.