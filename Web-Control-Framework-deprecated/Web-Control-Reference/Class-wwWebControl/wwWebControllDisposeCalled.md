Internal flag that determines whether Dispose has been called on this control. Used to avoid duplicate Dispose calls.

Any custom implementation of Dispose() should set this flag or have it set by the call to DoDefault().