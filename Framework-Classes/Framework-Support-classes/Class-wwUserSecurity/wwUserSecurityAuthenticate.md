This method is called to authenticate a user. This method can be overriden to provide custom functionality.

If the user logon is successful the user record (`.oUser`) is set to the selected user. If it fails the method returns .F. and returns a blank User record.

By default authentication occurs against a UserSecurity table specified in cFilename and cAlias. lCaseSensitive determines whether the username/password have to be case sensitive.
### Override this Method
This method is meant to be overridden to provide you with the ability to customize authentication that hits a different database or other store or uses different authentication logic. It's up to you.

Simply make sure you do the following:

* Set the `.oUser` property to a user record
* return .T. or .F.

It's important that the `.oUser` record is set and it includes the base fields that the existing structure supports as it is used in Web Connection's authentication operations.

Typically you'll end up extending the `.oUser` properties by adding additional custom fields in the underlying database table, which are automatically picked up in the `.oUser` structure when `SCATTER NAME` is called to populate the structure.