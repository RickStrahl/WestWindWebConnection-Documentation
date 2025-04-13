This is the method that performs authentication for the username and password passed via the form variables. The method by default uses a wwUserSecurity derived class to authenticate user and set the LoggedIn and IsAdmin properties.

This is also the worker method inside of the class that performs the Authentication. This is the method you'd have to override if you want to use your own class for Authentication.

**NOTE:**  
This method also doubles as an Event that you can override. You can override to create custom validation logic in the login routine. it should return .T. or false and is fired only when you click the login button.