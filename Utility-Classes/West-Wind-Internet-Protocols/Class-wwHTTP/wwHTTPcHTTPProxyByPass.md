This property allows you to specify a list of host names or IP Addresses that should not be routed through the proxy server. IOW, this list allows you to directly access the requested domains/IPs bypassing the proxy.

The following information is taken from MSDN on the InternetOpen() function:

**lpszProxyBypass **  
<blockquote>Address of a string variable that contains an optional list of host names or IP addresses, or both, that should not be routed through the proxy when dwAccessType is set to INTERNET_OPEN_TYPE_PROXY (3). The list can contain wildcards. Do not use an empty string, because InternetOpen will use it as the proxy bypass list. If this parameter specifies the "<local>" macro as the only entry, the function bypasses any host name that does not contain a period. If dwAccessType is not set to INTERNET_OPEN_TYPE_PROXY, this parameter is ignored and should be set to NULL. 
</blockquote>