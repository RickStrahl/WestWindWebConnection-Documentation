This method can be used to read values from the wc.ini file. 

Note that this method relies on the wcConfig key passed back from the wc.dll request to figure out the location of the INI file. This means this method is valid only during or after the first hit has occurred. Since this is a process method this is usually not a problem though.