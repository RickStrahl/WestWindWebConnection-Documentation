This function copies properties between two objects in a flat manner. Child objects or arrays are copied as object references and not copied.

If you have multiple level objects you can explicitly copy each of the child objects.

The function also provides a parameter to determine which object is used as the source object that is used for parsing the properties to update. This is especially useful if one of the objects in question is a COM object which cannot return property values from AMEMBERS().