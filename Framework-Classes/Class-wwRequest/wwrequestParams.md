Returns a value by checking FormVars, QueryString, Session and ViewState (in that order) for a matching key and returning the value.

This function is useful if you store values in the QueryString or in Form Variables and you don't want to explicitly check the value in each of the available collections.

Note though that this function is considerably slower than accessing the collections directly since all the collections are probed. The overhead occurs especially if keys don't exist in any of the collections.