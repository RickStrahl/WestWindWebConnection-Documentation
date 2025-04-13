Number of total pages for datasource based on the PageCount. This property gets set after DataBind() has been called and is set only if PageSize is greater than 0.

Typically you can look at this value in the PageIndexChanged event. This property is mostly used internally, but is exposed externally for you to use if needed.