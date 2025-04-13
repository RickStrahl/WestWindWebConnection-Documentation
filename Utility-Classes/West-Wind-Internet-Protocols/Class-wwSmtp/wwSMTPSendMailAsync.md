This method functions identical to the SendMail method of this component, except that the message is sent asynchronously in the background while the call to this function immediately returns to FoxPro.

There's no feedback on success or failure when this method is used. The method always returns .T. but there's no guarantee that the SendMail operation actually worked or even reached the server.