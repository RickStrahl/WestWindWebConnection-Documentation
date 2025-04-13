If you get the following IIS error:

**503 Service Unavailable**

you are likely running into a problem with an IIS Application Pool that is stopped and has to be manually restarted:

![](IMAGES/misc/ApplicationPoolStopped.png)

Application Pools are the IIS Hosting processes that host your Web applications and the host process can either be shut down manually and be manually taken down by stopping it individually.

> ### @icon-warning Restarting a shut down Application Pool
> When IIS shuts down an Application Pool - usually due to repeated failures - the App Pool will not restart unless you manually restart the Application Pool. Even a full restart of IIS will not restart a stopped Application Pool.

### Setting Rapid-Fail Protection
If you didn't manually stop the service it's possible that IIS explicitly shut down the Application Pool based on repeated failures which is determined by the **Rapid-Fail Protection** features in the Advanced Application Pool Settings configuration.

![](IMAGES/misc/RapidFailProtection.png)

You can try bumping these numbers up  bit to avoid having IIS shutting down your Application Pool due to repeated failures. Once stopped due to hitting these limits the Application Pool has to be manually restarted explicitly - even an IIS Reset will not start the App Pool.

### Determining why an App Pool was shut down
If IIS forced a shut down due to repeated failures, you'll want to try and find the source of the problem as it is likely a severe issue. Your best place for finding more info about errors are in the Windows Application Event log, which should provide further info on failures.