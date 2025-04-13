Returns an object with information about monitors and screen sizes of the system. 

The object contains the following properties:

* Monitors - number of monitors attached
* VirtualWidth - width of multiple screens
* VirtualHeight - height of multiple screens
* ScreenWidth - width of active screen
* ScreenHeight - height of active screen

This function is useful to detect multiple screens and detect when trying to load a form onto a monitor that is no longer available. For example when screen locations are saved and a monitor is no longer attached upon loading this function can be used to detect the location mismatch and move the location.

Also see the FixMonitorPosition() function which can automatically fix common problems with multiple screens disappearing.