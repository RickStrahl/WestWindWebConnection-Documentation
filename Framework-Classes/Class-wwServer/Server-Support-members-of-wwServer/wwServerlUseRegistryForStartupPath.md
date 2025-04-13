This property is used to determine whether Web Connection looks in the registry to find its startup path. This method affects the behavior of the [wwServer::GetAppStarPath()](vfps://Topic/_S840OZ98E) method.

When true (the default) Web Connection looks up the registry key for this specific server in the registry and uses that path. The default behavior then changes path to this directory and runs from there. 

When false, Web Conneciton ignores this key and always uses the EXE's startup directory.

The registry key is used to allow remote machines to launch against the configuration INI of an application on a remote machine. The idea being that you can run the EXE on a remote machine, but point at the startup path on the Web server.