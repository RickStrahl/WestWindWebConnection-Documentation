Allows you to create an Enum value that can be passed as a parameter.

Although wwDotnetBridge includes enum conversion routines these values are translated into numbers and passed. While that works in most cases, it doesn't if the enum parameters are part of an overload method or constructor.

In these cases you can use ComValue to create an enum and then use that value instead.