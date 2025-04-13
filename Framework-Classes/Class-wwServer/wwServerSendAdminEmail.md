Sends an admin email to the specified recipients.

This is the main method that is used to send system emails from Web Connection, and it is called for error handling operations and admin send operations. Forwarded to by `wwProcess::SendAdminEmail()` with the same signature.