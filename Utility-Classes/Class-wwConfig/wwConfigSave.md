Persists the current Config object either to an INI file, XML file or the registry. 

Depends on the cMode ("INI","XML"*,"REGISTRY") property to determine which to persist to.

The appropriate properties must be set:

**INI, XML**  
cFilename property is set.

**INI**  
cSubName - section name (ie. [wwdemo]). Example: *wwDemo*

**REGISTRY**  
cRegPath and cRegNode.