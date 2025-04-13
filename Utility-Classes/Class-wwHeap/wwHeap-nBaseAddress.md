Stores the base memory pointer address of where the Heap is allocated which can be used with SYS(2600) to retrieve data from this allocated memory.

This value is `-1` by default, and set when `Allocate()` is called - directly or indirectly from `SetData()`.

You can use this address to manually retrieve data from the heap with `SYS(2600)` instead of `.GetBytes()` which is easier to use but less efficient.

```foxpro
lcData = SYS(2600, loHeap.nBaseAddress -1 + lnOffSet, lnCount)
```