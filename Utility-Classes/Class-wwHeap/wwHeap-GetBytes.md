Retrieves a number of characters or bytes from the allocated heap at a given position. The position is 1 based.


> If the highest level of performance is needed to retrieve data, it's considerably more efficient to call the contained SYS(2600) function directly from your calling code.
> ```foxpro
> lcData = SYS(2600, loHeap.nBaseAddress -1 + lnOffSet, lnCount)
> ```