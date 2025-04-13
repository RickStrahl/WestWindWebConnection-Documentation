The internal reference of the raw object that holds the actual values for this dynamic instance. 

This is an internal feature and useful only for low level or serialization purposes.

### Why and How?
wwDynamic works by basically forwarding all property and method access to the `__Reference` instance which is internally an instance of an `EMPTY` object or - in the case of a child object - another instance of `wwDynamic`.

In some high performance or serialization situations it can be useful to access the underlying raw object reference as access to it will be faster than through the `wwDynamic` wrapper and provides raw FoxPro object access which might be required to accurately serializing the object.  For example, the `wwJsonSerializer` explicitly checks for `wwDynamic` instances and when it finds one, serializes the `__Reference` instance so a proper serialization graph can be built.