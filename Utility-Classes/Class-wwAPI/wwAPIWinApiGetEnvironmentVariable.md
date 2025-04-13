Returns a Windows Environment Variable. Unlike `GETENV()` it can return strings larger than 255 characters (specifically the Windows path!)

```foxpro
lcPath = WinApi_GetEnvironmentVariable("PATH")
```

Note: This is a static function not a class method