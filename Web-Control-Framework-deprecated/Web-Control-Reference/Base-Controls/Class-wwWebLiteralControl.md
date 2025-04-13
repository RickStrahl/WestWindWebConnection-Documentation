Similar to the Label control but has no markup properties. This control is the most efficient control to inject content into a page as it takes the text and inserts it into the control as is.

<div class="notebox">**Text only Rendering**  
Unlike other controls this control will ONLY render its text property content. It will not render styles, id's or anything else - just the raw text. This control is also optimized and does not fire any of the standard events. Visibility also has no effect on it. It just renders static text.</div>

This is the most basic control that the framework provides. The framework itself uses this control to render all static page text (ie. text that is not inside of controls) so every page in fact uses this control extensively.

Use the literal control for all dynamic content that needs to embed raw HTML/Text into the page without any special formatting. Use the Label control if you need more programmatic control over the content once it's rendered such as changing the style, formatting or other display attributes.