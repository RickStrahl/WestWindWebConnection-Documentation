This class and the `Markdown()` helper function, provide Markdown parsing for converting **Markdown** text **to HTML**.

In it's simplest for you can do:

```foxpro
DO MarkdownParser && Load Library (once)
lcMarkdown = "This is **bold** text and this is [linked](https://west-wind.com)."
lcHtml = Markdown(lcMarkdown)
```

The helper function is the preferred way to use the Markdown parser as it caches the Markdown parser rather than recreating the Markdown processing pipeline for each request.

Alternately you can explicitly use the `MarkdownParser` or `MarkdownParserExtended` classes, which give a little more control over various options:

```foxpro
lcMarkdown = "This is **bold** text and this is [linked](https://west-wind.com)."

loParser = CREATEOBJECT("MarkdownParser")
loParser.lSanitizeHtml = .T.
llUtf8 = .T.  && encode markdown to UTF8 before sending to Parser
loParser.Parse(lcMarkdown,llUtf8)
```