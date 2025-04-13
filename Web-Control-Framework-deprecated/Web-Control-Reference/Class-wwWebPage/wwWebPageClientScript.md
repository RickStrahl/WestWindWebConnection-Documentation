The ClientScript collection holds all client scripts, script includes (<script> links) and code loaded CSS links. This property generally should not be manually accessed, but rather is manipulated through RegisterClientScriptBlock, RegisterClientScriptInclude and RegisterCssInclude.

ClientScript blocks are injected after <form> tag and so render towards the top of the page. Script includes render depending on their location specified either in the top or bottom of the header or below the <form> tag.

You can loop through the collection of scripts, but note that some items may have location prefixes:
<ul>
* src - Script include
* srcheader  - Script include rendering at bottom of header
* srcheadertop - Script include rendering at the top of header
* no prefix - script block
</ul>

These are used internally to figure out how to render each item.

Note that StartupScripts are stored in their own StartupScript collection.