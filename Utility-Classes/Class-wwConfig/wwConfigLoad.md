Loads a persisted object from an XML file, INI file or the registry.

Relies on the cMode ("INI","XML"*,"REGISTRY") property to determine how to load the object.

Requires that the appropriate properties are set:

**INI, XML**  
cFilename property is set.

**INI**  
cSubName - section name (ie. [wwdemo]). Example: *wwDemo*

**REGISTRY**  
cRegPath and cRegNode.