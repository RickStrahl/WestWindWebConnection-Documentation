The HtmlDataGrid() function supports automatic paging. If you set the loConfig.PageSize to anything other than 0 the DataGrid Pager will automatically be rendered and links on the pager will automatically generate paging Urls for you.

It's important to understand that clicking the paging links will refresh the page with a GET operation. The pager creates links that look like this:

`YourPage.wwd?Page=2`

You can either set the loConfig.PageBaseLink property or have it set for you based on the currently running URL. The pager will add a query string value of Page (by default, but you can change it loConfig.PageQueryStringName) to the URL and navigate to that link via an HREF link.

Because the Pager reloads the page as a GET operation it's important to understand that **any form data on the same page might be lost unless first submitted when clicking on a pager link!**  

This is a limitation of the automatic paging provided by the pager. Therefore it's often good practice to keep paging grids to pages that provide only read-only data for selection or further click through.