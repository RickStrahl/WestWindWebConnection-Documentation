PreserveProperty is used to save property values between PostPacks using ViewState. 

This method lets you easily persist a control's property value in ViewState automatically. Note that the value persists only on the immediate PostBack so if you always want to persist a property you should put the call to PreserveProperty into the OnLoad() or other event that ALWAYS fires.