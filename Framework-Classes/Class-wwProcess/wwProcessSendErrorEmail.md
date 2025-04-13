This method sends email to the assigned administrator that is configured in the Web Connection configuration in `app.ini`. Specifically it uses the various `AdminEmail*` values in the configuration to configure mail server, authentication and sender information. Emails are sent only if `Adminsenderroremail=On`.

This method is called internally for hard errors that occur in the default `wwProcess::Error()` handler. You can use the `llForce` parameter to explicitly force a message to be sent independent of that configuration flag which is useful for explicit emails. 

> #### @icon-warning Meant for Admin Emails Only
> You shouldn't use this method to send application level notifications to end users as this method uses Administrator sender information which is likely not the same account you'd be sending things like authentication, verification and confirmation messages. For those scenarios use `wwSmtp` directly or add a custom email method to your own Process or business classes.


> #### @icon-warning Messages are Sent Async
> The messages that are sent are sent asynchronously and there's no error checking on failures. So, please ensure that you check your email settings by either triggering an error with `AdminSendErrorEmail=on` or create an explicit method that sends a test message in your server to verify your emails are working and getting sent.