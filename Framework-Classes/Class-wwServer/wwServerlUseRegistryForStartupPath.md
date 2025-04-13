This property can be set to force Web Connection not to look in the registry for the startup path. This might be useful if you have multiple separate applications that use the same wwServer derived class names as the registry stores the wwServer classname as the registry key.

The internal sequence to set cAppStartPath is:
<ul>
* (internal) Init fires
* (internal) GetAppStartupPath is called (looks at registry by default - cAppStartPath is set)
* OnInit  (your code fires - cAppStartPath and cAppIniFile can be overridden)
* (internal) ReadConfiguration is called (the actual configuration file is loaded from cAppIniFile)
* OnLoad   (your code fires for any other configuration stuff typically VFP environment)
</ul>