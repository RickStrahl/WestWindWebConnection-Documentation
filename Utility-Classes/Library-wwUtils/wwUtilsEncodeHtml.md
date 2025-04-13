This method fixes up HTML for display. Takes HTML and XML tags and converts them to HTML displayable characters (&amp;lt; for < for example).

Note that this method only translates quotes, ampersands and angle brackets (<>"&).

This method should be used to make HTML safe for display in browsers. By encoding angle brackets the string becomes 'displayable' where otherwise it would render the embedded tags as HTML. It also helps with preventing cross-site scripting attacks which are created by embedding <script> links into text and echo'd back. HtmlEncoded text will display script tags rather than execute them.