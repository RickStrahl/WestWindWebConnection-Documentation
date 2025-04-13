Determines how Web Connection logs requests. 

* 0 - No Logging
* 1 - Minimal Request Logging
* 2 - Full Request Logging
* 3 - Full Request and Response Logging

For production it's highly recommended you turn logging off or use Minimal Request logging to avoid massive disk usage - log files get large quickly and can easily overrun FoxPro's 2gb size limit.

For development Full Request and Response logging is useful, as it allows you to look at incoming Request and outgoing Response data which is often very useful for debugging.

> #### @icon-warning Log File Management
> It's important to keep an eye on the Web Connection log file and make sure it doesn't get too big and approaches or exceeds the 2gb limit. Even minimal logging eats up a lot of data quickly. Either pack and reindex your log files regularily from the Web Module Admin page or have a system of backing up log files and then deleting them (Web Connection