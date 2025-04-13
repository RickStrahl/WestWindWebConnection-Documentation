A collection of wwValidationError objects that contain error information. 

You most commonly collect values into this object from `Request.UnbindFormVars()` or `wwBusiness::ValidationErrors` to display error information about specific binding/validation errors.

```foxpro
IF (Request.IsPostBack())
   poError.Errors = Request.UnbindFormVars(poReportParms)

   *** Manually add errors
   IF EMPTY(poReportParms.TenantId)
      poError.Errors.AddError("Please add a Tenant","TenantId")
   ENDIF

   IF (poError.Errors.Count > 0)
   	   poError.Message = poError.Errors.ToHtml()
   	   poError.Header = "Please fix the following form entry errors"
   ELSE
       poError.Message = "Ready to run Report."
       poError.Icon = "info"
       RETURN
   ENDIF               
ENDIF

```