Execution timeouts for the Web Server and your application can be a bit confusing because there are multiple places where timeouts are configured:

* Your Web Connection Configuration 
* IIS Configuration


### Web Connection Timeout Configuration
The Web Connection .NET and ISAPI handlers both have request timeouts associated with each implementation that returns when a request exceeds a certain number of seconds. 

This setting is set in the respective config files:

**web.config (.NET)**

```xml
<configuration>
  	<webConnectionConfiguration>
		<add key="Timeout" value="90"/>
	</webConnectionConfiguration>
</configuration>
```

**wc.ini (ISAPI)**

```ini
[wwcgi]
;*** Time to allow request to finish
;*** Process will be terminated after number of secs
;*** specified here.           REQUIRED 
Timeout=60
```

The value is set in seconds.

> **Note**: If this timeout is longer than the IIS timeout, this timeout will never fire

This setting purely controls the Web Connection processing, specifically how long the handler waits for your Web Connection server to respond with a result to the incoming request.

If the timeout is reached Web Connection stops waiting for a response and returns a Web Connection specific error page.


> #### @icon-info-circle What it does
> The Web Connection timeout causes execution of your request to be aborted before it completes. The actual requests run on a separate thread and when the watcher times out an error response is returned while the actual operation running on the thread continues to finish. If on the next request that requires a new server instance this instance is still running it is killed and a new one is started in its place.

### IIS ExecutionTimeout
On a lower level IIS also configures an `ExecutionTimeout` for an ASP.NET application. This timeout determines how long a request should wait before it completes.

**web.config**

```xml
<configuration>
    <system.web>
        <httpRuntime executionTimeout = "90" 
                     maxRequestLength="10000000"  requestValidationMode="2.0"/>
    </system.web>   
</httpRuntime>   
```
The value specified in `executionTimeout` is specified **in seconds**. This setting is specific the current IIS Applicaiton (virtual or site) that this `web.config` file belongs to. The default is 20 seconds.;   

If the timeout is reached IIS terminates the request and returns an IIS error response.

> #### @icon-info-circle Affects all Requests
> Although this setting is made in `<system.web>` which is the ASP.NET Configuration section, if an application uses ASP.NET in an Application Pool this setting is used **even in ISAPI requests**.

### Precendence
The IIS setting is a low level setting at the IIS level so if it is shorter than your Web Connection timeout it will kick in **before** the Web Connection timeout and shut the request down. 

IOW, shorter IIS configuration values always take precendence over longer Web Connection values.

Web Connection has its own value because the IIS configuration value is not easily accessible from within an application and because that value may actually be longer for some scenarios like file uploads that require more time than a typical data request.