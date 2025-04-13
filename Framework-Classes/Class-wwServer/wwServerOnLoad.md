This event hook is fired exactly once when the first request hits the server. 

It fires after the both `OnInit()` and `OnInitCompleted()` have fired, but it doesn't fire immediately after those events. Instead this event hook is only invoked once when the very first request hits the server.

Use this method to **setup your application's environment**. When this method is called the server has read all of its configuration settings so you can safely read values from the various configuration objects. For this reason this method, rather than [OnInit()](vfps://Topic/_S7E1EZCT3) should be used to set any application specific settings.

Use OnLoad() to set up the application environment including SET PATH, any system SET commands. Use OnLoad() to adjust  the server's operational properties, to add properties or objects, load class libraries etc. Anything application specific should be managed here.