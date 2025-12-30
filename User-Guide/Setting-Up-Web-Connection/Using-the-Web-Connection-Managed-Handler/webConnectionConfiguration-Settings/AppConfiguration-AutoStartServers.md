Determines whether servers are automatically started when the first hit comes into the module **in File Mode** (COM servers **always** auto-start). 

This flag also determines several related behaviors:

If file based requests time out Web Connection checks to see if the specified number of servers in ServerCount is still active. If the number is smaller the difference of servers is loaded automatically.