Puts the current process thread to sleep for the specified number of milliseconds.

Pass llUseDoEvents for longer waits which waits for 100ms stretches interspersed with `DOEVENTS` that try to keep the FoxPro UI somewhat responsive while waiting (ie. no *White Screen of Death*).

Regardless of using this flag - it's best not to wait for more than a few seconds here to avoid UI jankiness.

> Note: This is a static function not a class method