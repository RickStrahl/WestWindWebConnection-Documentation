This is a FAQ Entry to track an issue discovered by Thierry Nivelet in this thread:

[Not enough memory' error when loading COM servers](https://support.west-wind.com/Thread6RR0G0WW2.wwt#6RZ0OWYMU)


### Issue Summary
When a lot of COM servers are loaded and those servers are heavily using memory (specifically loading many FoxPro Forms in Thierry's case), the COM servers would hard fail to load with out of memory errors.

The errors reported are FoxPro out of memory errors:

* when instantiating a form class : Error #43 "There is not enough memory to complete this operation"
* when instantiating an imageList OLE class : Error #1426 "OLE error code 0x80004005: Unspecified error"

This issue might be application specific but the error only started to occur under Windows Server 2022 initially after running fine on Windows 2012.

### Resolution
The fix for this tentatively appears to be a system level tweak that changes how DCOM Impersonation is handled. According to Thierry:


* COM Servers were failing when using the **[Launching User](https://learn.microsoft.com/en-us/windows/win32/com/launching-user)** in DCOM Identity
* COM Servers were not failing when explicit assigning **[This User](https://learn.microsoft.com/en-us/windows/win32/com/specified-user)** using the same identity in IIS

According to docs referenced there is a difference in how these user context are maintained. Using **This User** creates a brand new user context, while the **Launching User** inherits the user context that the IIS AppPool creates which may have some account limits applied to it (likely matching the AppPool memory limits). The new user context for **This User** gets created outside of IIS so there are no restraints and this resolved the out of memory errors.

### Remarks
The issue described here should be a very rare situation!

Thierry's application is an extreme case of heavy memory usage for FoxPro, as FoxInCloud relies on actual FoxPro form instantiation as well as loading ActiveX controls inside of the FoxPro process which are very heavy in memory usage. In addition this particular app is loading 12+ simultaneous instances. Typical Web Connection applicatinos using HTML based output via MVC or manual output generation (and even using the deprecated Web Control Framework) are unlikely to put out this much memory pressure for the application to run out of memory.

However, if you run into mysterious out of memory errors, this may be a useful thing to try.