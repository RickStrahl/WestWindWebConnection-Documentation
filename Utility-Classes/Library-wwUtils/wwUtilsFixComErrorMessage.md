Fixes up a COM Error message by stripping off the OLE Dispatch error message in the front of the string if it's present.

Turns:

```text
OLE IDispatch exception code 0 from wwDotNetBridge: Invalid extension `attridbutes` from `attridbutes`"
```

into:

```
Invalid extension `attridbutes` from `attridbutes`"
```