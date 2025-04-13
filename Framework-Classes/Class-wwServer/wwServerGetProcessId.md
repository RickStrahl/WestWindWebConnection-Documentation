Called by the ISAPI extension to finalize the initialization process.

This method returns the current process's ID. It also doubles as a startup hook that is called when the server is created
by the ISAPI DLL when running COM. 

It also handles cascading the server windows.