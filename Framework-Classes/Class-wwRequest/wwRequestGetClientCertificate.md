Returns the contents of a client Certificate's Subject key. This information contains the info about the client. The following information is provided:

ClientCert Flags=1
ClientCert Cookie=b0c24e793b9f11367c8ec916101b931e
ClientCert Subject=
L=Internet, O="VeriSign, Inc.", OU=VeriSign Class 1 CA - Individual Subscriber, OU="www.verisign.com/repository/CPS Incorp. by Ref.,LIAB.LTD(c)96", 
OU=Digital ID Class 1 - Microsoft Full Service, 
CN=Rick Strahl, E=rstrahl@west-wind.com

You can retrieve the Flags and Cookie with the [ServerVariables()](vfps://Topic/wwRequest%3A%3AServerVariables) method. This method only retrieves information in the Subject key.