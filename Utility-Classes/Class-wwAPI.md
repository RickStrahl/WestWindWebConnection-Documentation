The API class provides a number of useful utility functions that access the Windows API or other external DLL interfaces. This class also houses the wwImaging wrapper functions that talk to the wwImaging.dll.

Note that some of the functionality in this class is implemented as plain functions, rather than methods of the class. This is to avoid having to declare a class. None of the wwAPI classes actually contain any sort of state and would be better served as plain functions, but due to legacy codebase requirements they are part of the class.

>**Tip:**  
>In Web Connection Process methods, wwAPI is always available via Server.oAPI.