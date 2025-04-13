This method works like FoxPro's ADDPROPERTY() that automatically adds the property name specified to the `PropertyNameOverrides` so the case is preserved during serialization. 

This reduces the overhead in code having to explicitly set the property names in proper case when creating dynamic objects.

Note that this method is not required to create `serializable` objects - any FoxPro object instance will work. However, it's quite common to create dynamic structures at runtime in order to send data to servers that have to match a specific structure. Use this function if you were already using `ADDOBJECT()` and were manually setting `PropertyNameOverrides`. If you are using concrete classes created with DEFINE CLASS, this mechanism is not applicable.