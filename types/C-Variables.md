---
nav-title: "C Vars"
title: "C Vars"
description: "Describes how C variables are exposed."
position: 0
---

# C Variables
C variables are available in JavaScript as JavaScript variable.
Consider the following C declaration:
```objective-c
// NSObjCRuntime.h
FOUNDATION_EXPORT double NSFoundationVersionNumber;
```

It will export a JavaScript variable called NSFoundationVersionNumber:
```javascript
console.log("iOS version: " + NSFoundationVersionNumber); // "Version: 1141.1", the version number will differ
```

## Defines are not Available
There are limitations though. We do not have access to APIs that depend on the preprocessor. For example the following **will NOT be exposed**:
```objective-c
#define NSFoundationVersionNumber_iOS_6_1  993.00
```

The preprocessor will replace the occurrences with NSFoundationVersionNumber_iOS_6_1 with 993.00 in C but we do not have that information available in JavaScript. You will have to define variables with these constants on your own:
```javascript
var NSFoundationVersionNumber_iOS_6_1 = 993.00;
if (NSFoundationVersionNumber_iOS_6_1 <= NSFoundationVersionNumber) {
	// iOS 6.1 or up.
}
```

## Anonymous Enums
The members of anonymous enums are exposed as variables in JavaScript similar to C variables.



