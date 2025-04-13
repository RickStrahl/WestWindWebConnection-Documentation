The event is called when the server is released it's called explicitly but also from the destroy. If you have any references you've created that might be circular or otherwise need cleaning up it's recommended you override this method and clean up the code.

Make sure to call `DODEFAULT()` to call the base class code.