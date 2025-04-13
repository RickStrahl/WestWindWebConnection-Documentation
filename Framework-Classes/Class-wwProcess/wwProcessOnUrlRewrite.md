Use this method if you're using[ URL rewriting to create extensionless URLs](VFPS://Topic/_3K812UDQ3). This method can intercept request routing and take action based on the extensionless path segments.

Note that this method depends on a rewrite rule being in place, and `wwProcess::UrlRewriteHandler` getting fired by the rewrite rule.