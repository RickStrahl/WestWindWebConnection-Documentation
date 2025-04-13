Creates a JSON encoded string from a FoxPro string including the wrapping quotes.

A common use case is to embed JSON variables into HTML documents:

```html
<% section="scripts" %>

model = {
 openCommentBox: <%= JsonBool(poModel.lOpenCommentBox) %>,
 appName: <%= JsonString(poModel.cApplicationName) %>,
 startOn: new Date("<%= JsonDate(poModel.tStartDate) %>")
};
<script src="ShowEntry.js"></script>

<% endsection %>
```