Allows creation of an type directly from a file without having to first call `LoadAssembly()`. 

Generally this is not recommended except for special occasions, it's almost always better to use `LoadAssembly()` first, then call `CreateInstance()`.