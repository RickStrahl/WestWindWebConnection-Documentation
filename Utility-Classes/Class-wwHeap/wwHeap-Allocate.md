Allocates a block of data on the heap. Specify a size for the block to allocate.

> If you want to just 'fill' the heap from some specific data use `SetData()` which performs both allocation and assignment in one pass **if no previous allocation has been made** (`.nBaseAddress = -1`)