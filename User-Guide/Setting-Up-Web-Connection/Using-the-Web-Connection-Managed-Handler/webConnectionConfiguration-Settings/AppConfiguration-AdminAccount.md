The AdminAccount key sets the account that is allowed access to the administration functions in the Web Connection .NET or ISAPI Handler.

The following values can be set:

* **ANY**  
Any **authenticated** account. This is the default value.
* **accountName**  
A specific user account name
* **accountName, accountName2**  
Comma delimited list of account names
* **empty**  
No value means **every one** can access (*not recommended*). 


> #### Windows Home Editions
> Note that the various Windows Home Additions don't have support for Windows Authentication that maps logins to Windows accounts. If you are running Windows Home set the `AdminAccount` value to **empty** (ie. no value is set).