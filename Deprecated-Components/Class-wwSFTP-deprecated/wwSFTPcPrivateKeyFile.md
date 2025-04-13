An optional private key file to pass instead of or in addition to a password. Path should be fully qualified.

> #### @icon-warning Private Key should be in OpenSSH format
> The private key file should be in OpenSSH format that starts with a  
>`-----BEGIN RSA PRIVATE KEY-----`  
>prefix. If your file is a PutTyGen key file, you can use <a href="http://www.putty.org/" target="top">PutTyGen</a> to convert it to OpenSSH format. 

Most SFTP servers that use Private Key Files expect both a password and keyfile for two-factor authentication, but some servers only require one or the other. Check with your server documentation what's required.