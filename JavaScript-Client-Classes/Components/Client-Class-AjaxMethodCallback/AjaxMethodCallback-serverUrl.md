The Url that is to be called on the server. This value is typically set by the Constructor call.

The value can be a relative URL so the following is valid:
<ul>
* MyPage.aspx
* admin/MyPage.aspx
* http://www.west-wind.com/wwHoverPanel/callbacks.aspx
</ul>

Note that Web site external URLs will either fail or cause security popups since XMLHttp has security restrictions to sending content to other sites. This functionality can be configured in the browser, but it's not recommended. Stick to local site calls, and if you need to make external calls use server side code to make the remote call and then return the result to the client.