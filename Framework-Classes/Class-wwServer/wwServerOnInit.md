This method is called at the very beginning of the server's initialization sequence and should make no assumptions about any server state. 

Use this method to override the startup folder and the location of the startup configuration, but little else. 

Note you should keep the code in OnInit minimal. You'll get another chance to configure your application after the server has loaded its configuration state in `OnLoadCompleted()` which fires after the server settings have been initialized, and `OnLoad()` which fires on the first request to the server.

We recommend most configuration tasks are performed in the `OnLoad()` to maximize server startup performance in COM mode. There preferrably should be **no application logic code** inside of `OnInit()` or `OnInitCompleted()`