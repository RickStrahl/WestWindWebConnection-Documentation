The following lists can be pasted in to be used with various VFP objects to write out only custom 
properties of the objects in question. You can customize these lists if you choose to update
specific native properties such as form captions for example.

You can simply cut and paste these filters from here into your code as needed.

**Custom:**  
<pre>THIS.cPropertyExclusionlist = THIS.cPropertyExclusionList + ;
"baseclass,class,classlibrary,comment,controlcount,controls,height,helpcontextid,left,name,objects,"+;
"parent,parentclass,picture,tag,top,whatsthishelpid,width,"
</pre>

**Relation:**  
<pre>THIS.cPropertyExclusionlist = THIS.cPropertyExclusionList + ;
"baseclass,childalias,childorder,class,classlibrary,comment,name,onetomany,parent,parentalias,parentclass,"+;
"relationalexpr,tag,"
</pre>

**Forms:**  
<pre>THIS.cPropertyExclusionlist = THIS.cPropertyExclusionList + ;
"activecontrol,activeform,alwaysonbottom,alwaysontop,autocenter,backcolor,baseclass,borderstyle,"+;
"buffermode,caption,class,classlibrary,clipcontrols,closable,colorsource,comment,continuousscroll,"+;
"controlbox,controlcount,controls,currentx,currenty,datasession,datasessionid,defolelcid,desktop,"+;
"drawmode,drawstyle,drawwidth,enabled,fillcolor,fillstyle,fontbold,fontcondense,fontextend,fontitalic,"+;
"fontname,fontoutline,fontshadow,fontsize,fontstrikethru,fontunderline,forecolor,halfheightcaption,"+;
"height,helpcontextid,hscrollsmallchange,hwnd,icon,keypreview,left,lockscreen,macdesktop,maxbutton,"+;
"maxheight,maxleft,maxtop,maxwidth,mdiform,minbutton,minheight,minwidth,mouseicon,mousepointer,"+;
"movable,name,objects,oledragmode,oledragpicture,oledropeffects,oledrophasdata,oledropmode,parent,"+;
"parentclass,picture,releasetype,righttoleft,scalemode,scrollbars,showintaskbar,showtips,showwindow,"+;
"sizebox,tabindex,tabstop,tag,titlebar,top,viewportheight,viewportleft,viewporttop,viewportwidth,"+;
"visible,vscrollsmallchange,whatsthisbutton,whatsthishelp,whatsthishelpid,width,windowstate,"+;
"windowtype,zoombox,"
</pre>

<blockquote>**Tip:**  
Using the form filter can be really useful to save settings of a form if you have attached properties
for the individual controls of a form. For example if you have a property like cFilename and bind that
to a text box you can restore the defaults very easily using wwXML ObjectToXML/XMLToObject and
saving the XML to a file. Of course an even cleaner way is to bind to a business object and saving
the business object in this fashion.</blockquote>