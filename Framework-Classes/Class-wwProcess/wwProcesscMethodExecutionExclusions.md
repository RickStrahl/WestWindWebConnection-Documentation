Allows exluding of class methods that are executed as methods on the process class. This allows executing of script pages even if a matching method exists on the wwProcess subclass.

This method is used to override methods like Login and Process to ensure these are not fired as methods, but CAN be run as script pages.

Default Value:
",login,process,"