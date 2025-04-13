Determines whether servers are automatically started when the first hit comes into the module. 

This property is used only for file based operation and determines whether file base instances are loaded when the module starts for the first time.

This flag also determines several related behaviors:

If file based requests time out Web Connection checks to see if the specified number of servers in ServerCount is still active. If the number is smaller the difference of servers is loaded automatically.