Creates a child object instance that inherits the properties from the current object appropriate from the current object instance.

For instance when SQL data (nDataMOde is used) the oSQL member is inherited and shared. On Web data (nDataMode=4) the OHTTPSQL member and the cServerUrl are passed down. This method is used to simplify building nested hierachical objects and reduce the code required to handle different kind of data access mechanisms.

Use this method instead of CREATEOBJECT() for childobjects.