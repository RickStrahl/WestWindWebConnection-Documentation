This helper **function** creates a cached `wwRegEx` object. Because it caches globally it is much faster on repeated iterations.

> Note: This a global function in `wwRegEx.prg`, not a method of the class.
>
> Make sure the .prg is loaded with `SET PROCEDURE TO wwRegEx additive` before calling this function.