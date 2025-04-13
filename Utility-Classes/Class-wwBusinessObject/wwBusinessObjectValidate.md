Virtual method that can be used to hook up validation logic for your business objects. Validation typically gets called before saving an entity.

You override this method and fill the `oValidationErrors` property with any errors you find. The method should then return `.T.` or `.F.` to determine whether validation errors where found.

This method needs to be called explicitly unless lValidateOnSave is set to .T. This method returns .T. by default if not implemented.