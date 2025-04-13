Creates a FoxPro collection from a ComArray for easier access in FoxPro code, when the collections are read only. 

Primarily meant for internal functions that return Collection results that don't need to be updated.

> ##### @icon-info-circle Collections should only be used for Read Only Operations
> If you plan on updating the Collection, do not use this method. Instead use the COM Array to retrieve instances and update, remove, add items as needed through the ComArray interfaces.