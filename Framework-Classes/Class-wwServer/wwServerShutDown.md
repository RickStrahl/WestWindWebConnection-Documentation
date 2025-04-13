Shuts down the server in **File Mode**. 

Internally issues a **CLEAR EVENTS** followed by **QUIT**. This method is internally called from Windows `WM_CLOSE` message calls initiated when FoxPro or your Application is closed from the Task bar, Close box or via 'Close' operations in Task Manager etc.