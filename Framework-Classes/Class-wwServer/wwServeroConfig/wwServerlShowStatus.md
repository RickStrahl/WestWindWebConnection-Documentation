Determines wether progress information is shown in the server dialog. 

If you are running in production we recommend you turn this off as there's significant overhead in displaying output in the window and in most production environments there's no server active to actually display output - however the output overhead is still incurred as VFP internally draws the windows whether they're visible or not. 

If running under COM and any non-interactive user account always turn this off.