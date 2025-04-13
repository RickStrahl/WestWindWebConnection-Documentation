A wrapper class around the Windows Heap that allows creating a buffer of character or byte data that you can access quickly even for very large buffers. 

This class specifically allows for iterating over strings or byte buffers one byte at a time as required by parsers and encoders. It helps works around the extreme slowness of FoxPro's `Substr()` function with anything but small buffers ([more info](https://west-wind.com/wconnect/weblog/ShowEntry.blog?id=962))

```foxpro
DO wwAPI && load library

LOCAL loHeap as wwHeap
loHeap = CREATEOBJECT("wwHeap")

lnIterations = 10000000   && 10 million characters
lcData = REPLICATE("0123456789",lnIterations / 10)

* loHeap.Allocate(LEN(lcData))    && Not needed if you set the size same as data
loHeap.SetData(lcData)            && Sets and Allocates if not allocated yet

*** Using built-in GetBytes() method
lnStart = SECONDS()
FOR x = 1 TO lnIterations
   lcChar = loHeap.Getbytes(x,1)   && retrieve 1 char at the time      
ENDFOR
? SECONDS() - lnStart  && 5.0 seconds

loHeap.Release()
```

### For Improved Performance in Tight Loops bypass `.GetBytes()`
`GetBytes()` is the explicit method to retrieve data from the heap, but it turns off the method wrapper adds significant overhead to retrieval times. If you need the highest performance it's more efficient to call the `SYS(2600)` function directly to access the memory.

The above code can be changed to:

```foxpro
*** Direct Access without GetBytes() is much faster!
lnStart = SECONDS()
FOR x = 1 TO lnIterations
   *** No method call	
   lcChar =	Sys(2600,loHeap.nBaseAddress-1 + x,1)
ENDFOR
? SECONDS() - lnStart   && 1.1 seconds
```