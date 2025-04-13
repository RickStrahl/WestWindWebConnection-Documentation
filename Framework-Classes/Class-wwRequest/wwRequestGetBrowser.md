Returns the client's browser display name.

The names tend to be verbose and do not lend themselves well for parsing and consistent values. For example IE 5 returns:

**Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)**  

In order to do some useful browser detection you probably have to do a little more work. For example to check for IE you typically check for the MSIE string or the IE version MSIE 5.