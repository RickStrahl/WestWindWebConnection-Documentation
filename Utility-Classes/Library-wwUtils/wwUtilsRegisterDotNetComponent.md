Registers a .NET Component for COM operation by calling regasm /codebase. 

.NET Components require a special COM registration mechanism beyond regsvr32 as they require to register to .NET framework DLL as the COM object and the component to invoke using separate keys.