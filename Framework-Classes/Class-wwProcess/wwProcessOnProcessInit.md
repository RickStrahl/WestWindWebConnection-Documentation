The OnProcessInit method is a hook that can be used to initialize a process class. Here you can create custom objects, run additional configuration tasks that apply to every request that hits this process class.

This method is called from the beginning of the Process() method call after the various PRIVATE objects (Response, Request, Server, Session, Config) have been assigned. 

This method should be used instead of overriding the Process() method in previous versions.  Note that you cannot declare PRIVATE variables visible to Process methods later here as this method doesn't stay at the top of the call stack. If you need to declare PRIVATE variables visible to your Process method code, you still have to override Process. In this case we recommend you do this:

```foxpro
FUNCTION Process

* ** Use Process() overrides only to declare PRIVATE vars
* ** otherwise use OnProcessInit()
PRIVATE CustomVar

CustomVar = CREATEOBJECT("MyCustom")

DODEFAULT()

ENDFUNC


FUNCTION OnProcessInit()

* ** Any process configuration code
THIS.InitSession("MyCookie")

IF !THIS.Login("ANY")
	* ** Stop processing
	RETURN .f.
ENDIF

* ** Continue processing
RETURN .T.
ENDFUNC
```