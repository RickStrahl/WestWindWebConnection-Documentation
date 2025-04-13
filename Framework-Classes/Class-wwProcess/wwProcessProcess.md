Use of this method is deprecated. You should use [OnProcessInit](vfps://Topic/_1LS0UIHYW) instead.

The Process method is the core processing method of the wwProcess class that initiates and completes request processing. It does all of the work.

Use this method only if you need to declare PRIVATE variables that need to be visible to your lower level Process methods. Whenever you call this method make sure you call DoDefault() to handle core processing:

```foxpro
FUNCTION Process
PRIVATE MyCustom
MyCustom = CREATEOBJECT("MyCustom")
DODEFAULT()
ENDFUNC
```