Splits a string into a collection of strings based on a specified size. Used internally to split up strings greater than 16mb into smaller chunks that can be concatenated seperately.


The following example allows writing out strings larger than 16mb via the `lcHttp` variable:

```foxpro
LOCAL lcResponse, lcHttp, lnX

*** > 16mb string potentially - can't concatenate header before value
lcResponse = this.oResponse.Render(.T.)

*** Start creating  http.
lcHttp = this.oResponse.RenderHttpHeader()

*** Can't append >16mb string to lcHttp so chunk it
IF LEN(lcResponse) > 15800000    
   LOCAL loCol as Collection
   loCol = SplitStringBySize(lcResponse,5000000)  && 5mb chunks
   FOR lnX = 1 TO loCol.Count
       lcHttp = lcHttp + loCol.Item(lnX)
   ENDFOR
   
   *** lcHttp larger than 16mb now!
ELSE    	
   lcHttp = lcHttp + lcResponse
ENDIF
```