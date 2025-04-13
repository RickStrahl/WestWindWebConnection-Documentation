Creates an email link on a Web page that is not easily harvested by breaking up the Url.

```html
<%= MailLink('rickstrahl@hotmail.com','Rick Strahl') %>
```

This generates a block of JavaScript that looks like this:

```html
<a href="javascript:_1RD1EEIV5('rickstrahl','hotmail.com');">Rick Strahl</a><script> function _1RD1EEIV5(eValue,Text) { alert('hello'); var c1 = 'ma'; var c2 = 'ilto:'; var Link = c1 + c2 + eValue + '@' + Text;window.open(Link);}</script>
```

which in turn opens the mail client.