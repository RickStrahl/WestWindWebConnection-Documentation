Key method for clearing state of a control.

It is crucial that every control calls Dispose() when shutting down, especially container controls. If Dispose() is not called it's very likely that there will be hung references with containers.

For details on implementing a custom Dispose() handler please see the implementation of wwWebControl::Dispose(), which demonstrates complete cleanup. In most cases it's sufficient to simple DoDefault() and use the default behavior, but you may have to override for any custom collections or other reference objects you define.