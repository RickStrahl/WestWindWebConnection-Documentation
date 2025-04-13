The CSS Class used in the Pager row of the table.

Note that the main pager's css class is .gridpager. This class is configurable by this property.

There are additional non-configurable styles that exist for the selected pager page, and the individual pager pages which are the a tags:

<ul>
* **gridpagerselectedpage**  
This class is applied to the selected page applied via <span> tag.

* **gridpagerpage**  
This class is applied to the <a> tag that links to each of the pages and the next/prev/first/last page links.
</ul>

These styles cannot be specified via properties, but they can be modified via customized CSS styles either in the current page or in the main stylesheet.