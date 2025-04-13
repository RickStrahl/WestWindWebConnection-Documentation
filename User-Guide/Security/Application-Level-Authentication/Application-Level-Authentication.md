Web Connection provides a number of ways to authenticate users through your application code:

* **[Windows Authentication](vfps://Topic/_S8W0RUIBB)**  
Uses Windows Accounts and an HTTP level protocol to validate users. Works well if your app is internal and you can easily add user accounts to Windows. 

* **[UserSecurity](vfps://Topic/_1P200A3D3)**  
Uses the wwUserSecurity class which uses FoxPro tables to manage basic user access. This mechanism uses plain HTML pages to display a login dialog or provides a wwWebLogin control to use on a page to check for authentication.

* **[File Level Windows Authentication and ScriptMaps](vfps://Topic/_1C1117DLT)**  
This mechanism allows you to use Windows Authentication based on file permissions on the physical disk. The permissions on the physical file of a page determine how Authentication occurs and who's allowed access to a page. This option requires that EVERY request in the virtual directory has a physical page on disk - any non-page backed requests will result in a 404 error.