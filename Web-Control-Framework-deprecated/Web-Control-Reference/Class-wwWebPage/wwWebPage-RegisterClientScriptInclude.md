Inserts a link to an external script file into the page. Generates <script src="scriptpage.js"></script> into the page.

Scripts can be injected into the page at various locations to allow more control over scripts and provide for better integration with manually added scripts. The three locations - headertop, header and script - correspond roughly to library, support/plug-in library, and application level locations.

Injected script tags are of the following form:
```html
<script src="/virtual/script.js" type="text/javascript" />
```