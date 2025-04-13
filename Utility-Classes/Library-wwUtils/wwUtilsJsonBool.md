Returns `true` or `false` for a boolean value. Very simple but a nice way in script pages to be explicit about what the value represents.

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