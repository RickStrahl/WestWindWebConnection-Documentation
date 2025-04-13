The following is an example using the HtmlDataGrid() function in a Process class using code only. This example uses custom column configuration to configure the DataGrid and then renders it straight into the Response.Write() output.

The output produced by this example looks like this:

![](IMAGES/stepbystep/HtmlDataGrid.png)

This example demonstrates:

* Using FoxPro expressions for rendering columns
* Custom column formatting
* Applying of custom HTML styles to columns
* Using the Paging Features (just set loConfig.Page > 0)

The code to accomplish this is shown in this complete Process class method:

```foxpro
FUNCTION ShowAlbumsPaged
************************************************************************
*  ShowAlbumsPaged
****************************************
***  Function: A simple code based data list using the 
***            HtmlDataGrid helper including paging
***    Assume:
***      Pass:
***    Return:
************************************************************************
LOCAL lcFilter

lcFilter = Request.Params("AlbumFilter")

SELECT albums.pk,albums.title, albums.year, ;
       artists.pk as artistpk, artists.artistName ;
    FROM albums, artists ;
    ORDER BY albums.title ;
	WHERE albums.ArtistPk = artists.Pk AND ;
	      artists.ArtistName = ?lcFilter ;	
    INTO CURSOR TQuery

*** Page setup - irrelevant to HtmlDataGrid
Response.WriteLn( this.PageHeaderTemplate("Album Listing"))
Response.WriteLn([<div class="page-header-text"><i class="fa fa-list"></i> " + ;
                 Album List <span class="badge">] + ;
                 TRANSFORM(lnCount) + [</span></div>])

 
*** Use the HtmlDataGrid Helper function (easier)
loConfig = CREATEOBJECT("HtmlDataGridConfig")

*** Paging is a problem when you dynamically switch between the two modes
*** so leave out for now
loConfig.PageSize = 6

*loConfig.Width = "600px"
loConfig.CssClass = "table table-striped small"

*** Add a column explicitly
*** Note this column uses an expression that is evaluated for each record
loColumn = loConfig.AddColumn([HtmlLink("ShowAlbum.wwd?id=" + TRANSFORM(Tquery.pk),Title)],"Album")
loColumn.Sortable = .T.
loColumn.SortExpression ="Upper(Title)"

loColumn = loConfig.AddColumn("ArtistName","Artist Name")
loColumn.Sortable = .T.
loColumn.SortExpression ="Upper(ArtistName)"

loConfig.AddColumn("Year","Year Released","N")

loConfig.AddColumn("HtmlCheckBox([chkIsActive_] + TRANSFORM(Pk) ,[],Year > 2000)","Recently Produced")

lcHtml = HtmlDataGrid("Tquery",loConfig)

Response.Write(lcHtml)
       
Response.Write( this.PageFooterTemplate() )   

ENDFUNC
```