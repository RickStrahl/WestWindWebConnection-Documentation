When set causes the COM server to be run in VFP unattended mode (SYS(2335,0)) which prevents any user interface operation from firing. This will prevent accidental file open dialog boxes (which are not trappable) and generate errors for any modal UI operations (like an accidental MessageBox somewhere).

Turn this option on for production installs and turn it off while debugging (if running in Interactive COM Mode).


When active it unattended mode is applied in the following scenarios:

<ul>
* **Server Init()**  
Unattended mode is turned on when running in COM mode during server initialization to allow capturing startup errors. If errors occur a TRY/CATCH handler captures the errors and logs them, but the server will fail to load. Errors are logged as to the server error log as part of the TRY/CATCH operation.

* **ProcessHit() for all Requests**  
Before each request is fired unattended mode is turned on and then turned off when the operation is complete. Any errors should be trapped 

</ul>

In general it's a good idea to set this flag to true, but be absolutely sure that the server does not bring up any UI that might cause a problem. Note that many UI operations - like non-modal forms, WAIT WINDOWS actually work in this mode.

This property only works in COM mode and has no effect in file mode.