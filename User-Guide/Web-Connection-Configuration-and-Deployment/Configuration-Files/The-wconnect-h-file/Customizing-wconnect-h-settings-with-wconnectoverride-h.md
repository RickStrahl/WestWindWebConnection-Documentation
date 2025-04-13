A new Web Connection installation overwrites your `wconnect.h` file along with any custom settings you've made to it, so **you should never make changes in wconnect.h** directly.

Instead Web Connection can use a `wconnect_override.h` file if it exists to **override** any settings in `wconnect.h` externally. The `wconnect_override.h` file **is not overwritten** and so any changes you make there are preserved through updates.

Web Connection does the following on the bottom of `wconnect.h`:

```foxpro
#IF FILE("WCONNECT_OVERRIDE.H")
	#INCLUDE WCONNECT_OVERRIDE.H
#ENDIF
```

Inside of `wconnect_override.h` you can then use `#UNDEFINE` statements and then `#DEFINE` any settings again for setting the overridden new value.

This looks something like this:

```foxpro
#UNDEFINE DEBUGMODE
#DEFINE DEBUGMODE 					.F.

#UNDEFINE MAX_TABLE_CELLS
#DEFINE MAX_TABLE_CELLS				20000

#UNDEFINE WWC_CACHE_TEMPLATES
#DEFINE WWC_CACHE_TEMPLATES			0

#UNDEFINE VISUALWEBBUILDER
#DEFINE VISUALWEBBUILDER 			.F.

#UNDEFINE WWC_USE_SQL_SYSTEMFILES    
#DEFINE WWC_USE_SQL_SYSTEMFILES     .F.

#UNDEFINE WWSTORE_USE_SQL_TABLES      
#DEFINE WWSTORE_USE_SQL_TABLES      .F.

#UNDEFINE WWMSGBOARD_USE_SQL_TABLES      
#DEFINE WWMSGBOARD_USE_SQL_TABLES      .F.
```

When an upgrade rolls around Web Connection will overwrite your `wconnect.h`, but the settings in `wconnect_override.h` are preserved.

### wconnect_override.h in multiple Projects
Note that `wconnect_override.h` is specific to each application/exe you build most likely with settings that are specific to that project. 

You can create a local `wconnect_override.h` in a project folder if you like which allows each project to have it's `wconnect_override.h`. 

While this is possible and it works, it can be confusing - most Web Connection `wconnect.h` are global settings so it's typically better to set them at the root Web Connection install folder level. But if you really need per project configuration - it's available. You may have to ensure that the local path `.\` is in your FoxPro path **before** the Web Connection install folder path to avoid looking there first:

```
PATH=.\;\wconnect\classes;\wconnect
```