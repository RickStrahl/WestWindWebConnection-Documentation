The API class provides a number of useful utility functions that access the Windows API or other external DLL interfaces. This class also houses the wwImaging wrapper functions that talk to the wwImaging.dll.

Note that the majority of functionality in this class is implemented as plain static UDF functions, rather than methods of the class. This is to avoid having to declare a class. 

However, several legacy features still exist in the so you have to ensure you use the write mechanism to access these helpers. None of the wwAPI classes actually contain any sort of state and would be better served as plain functions, but due to legacy codebase requirements they are part of the class.

* [wwApi Class Reference](dm-topic://_0dktrogfnw)

>**Tip:**  
>In Web Connection Process methods, wwApi is always available via      `Server.oApi`.