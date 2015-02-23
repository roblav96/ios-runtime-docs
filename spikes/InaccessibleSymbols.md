# Inaccessible symbols

The following symbols from iOS SDK can not be accessed from JavaScript because there is no metadata generated for them:

* All unions.
* All variadic Objective-C methods which have no `NS_REQUIRES_NIL_TERMINATION` attribute as part of their declaration.
* Variadic function pointers
* Variadic blocks
* Vectors
* `long double`, `int128_t`, `uint128_t`
