OnLoadControlState fires when the control tries to retrieve it's state in a POST back. The default implementation doesn't do anything - each control must implement its own functionality.

This method is called after OnLoadViewState().

Be sure to call DoDefault() especially on container controls!