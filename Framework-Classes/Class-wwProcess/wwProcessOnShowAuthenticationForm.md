Event that is fired when an authentication form should be displayed to prompt for login information. Hook this method to override the behavior of the user interface.

The form displayed should have the following form fields:

* WebLogin_txtUsername
* WebLogin_txtPassword
* WebLogin_chkRememberMe (not used currrently)


Form variables are forwarded to Authenticate() which verifies these form vars.