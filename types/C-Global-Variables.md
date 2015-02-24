---
nav-title: "C Global Variables"
title: "C Global Variables"
description: "Describes how C globale variables are exposed."
position: 0
---

# C Global Variables
C global variables are available in JavaScript as JavaScript global variables.
Consider the following C declaration:
```objective-c
// NSObjCRuntime.h
FOUNDATION_EXPORT double NSFoundationVersionNumber;
```

It will export a JavaScript variable called `NSFoundationVersionNumber`:
```javascript
console.log("iOS version: " + NSFoundationVersionNumber); // "Version: 1141.1", the version number will differ
```

## Macros are not Available
There are limitations though. We do not have access to APIs that depend on the preprocessor. For example the following **will NOT be exposed**:
```objective-c
#define NSFoundationVersionNumber_iOS_6_1  993.00
```

The preprocessor will replace the occurrences of `NSFoundationVersionNumber_iOS_6_1` with `993.00` in C but we do not have that information available in JavaScript. You will have to define variables with these constants on your own:
```javascript
var NSFoundationVersionNumber_iOS_6_1 = 993.00;
if (NSFoundationVersionNumber >= NSFoundationVersionNumber_iOS_6_1) {
    // iOS 6.1 or up.
}
```

## Anonymous Enums
The members of [anonymous enums](C-Enums.md) are exposed as global variables in JavaScript similar to C variables.
