Creates an HTML table from a cursor and returns the HTML as a string.

This implementation creates an HTML table grid layout that uses a CSS styling to control rendering and formatting for the table and columns. Automatic mode renders a table from existing data using meta data, or you can manually configure columns to add to the display.

The default styling uses styles in westwind.css: .gridheader, .gridalternate,.blackborder, all of which can be overridden via properties or by adding these styles to your own style sheets and changing the display features.

![](/images/stepbystep/HtmlDataGrid.png)