Object reference to a ready to use [wwResponse](vfps://Topic/Class%20wwResponse) object, which is used for all HTTP output. This object is preset by the Init method of this class. When Process or your custom methods get control the oResponse object is already set.

The default Process method also exposes this object as a PRIVATE variable *Response*.