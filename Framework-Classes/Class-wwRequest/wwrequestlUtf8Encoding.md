If .T. causes Request.Form() and Request.QueryString() to UTF-8 decode the returned values. 

Utf-8 encoding is off by default for pages, but if you switch the browser into UTF-8 mode or use AJAX and JavaScript Escape/EncodeUriComponent functions the values are automatically UTF-8 encoded. This option allows you to override the behavior and retrieve values in this format properly adjusted for the current character set.

If you set the property you should do it early on in the request cycle. In Web Control Pages or controls you should set this switch in OnInit() so it occurs before the automatic Request -> Control mapping occurs. For example, the wwWebAjax control does this as JavaScript escape() functionality always encodes strings as UTF-8 in addition to Url encoding.