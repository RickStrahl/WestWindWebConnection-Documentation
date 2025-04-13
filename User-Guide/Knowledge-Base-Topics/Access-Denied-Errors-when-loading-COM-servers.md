*When switching into COM operation I'm unable to get my server to come up. Instead I get an error message:*

> Couldn't instantiate <serverProgId>. CoCreateInstance failed: Access Denied.

This error message implies that the COM server could not be started because the DCOM permissions are insufficient to launch the server. If you used DO BLD_<server> to build your server most of the DCOM configuration settings were actually made for you. However, under Windows 2000 some additional one time settings that cannot be automated may have to be made in the DCOMCNFG utility.

* Go into the DCOMCNFG utility by type DCOMCNFG into the Windows Run box from the Start Menu.
* Go to the Default Security Tab.
* Add the following users: IUSR_<machinename> and IWAM_<machinename>. If you created protected IIS applications (ie. Isolated Application mode) you can also add IWAM_<uniqueappId>. 
* On a production machine you may not want to allow access for all COM objects, so instead of configuring the Default System Security, you can make those same settings on the particular server entry you're building (ie. wcDemo.wcDemoServer). Use the Security tab to override the default security.

In Windows NT 4.0 the IUSR_ and IWAM_ users were automatically available in the default security - in Windows 2000 I've seen no users at all in RC2, with the users there in RC1.

Note that the calling security context is determined by the setting of the IIS application. Default mode is Medium security (Pooled) which is accessed through IWAM_<machinename>. Low (IIS Native) is accessed through IUSR_<machinename>. High security is administered through IWAM_<appid> - you can check the custom app ID by looking in the Security tab of the application.