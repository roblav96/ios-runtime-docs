---
nav-title: "Objective-C Categories"
title: "Objective-C Categories"
description: "Describes how Objective-C categories are exposed."
position: 0
---

# Objective-C Categories
The Objective-C categories are powerful mechanism for extending existing Objective-C classes or grouping common API together.

Consider the NSStringPathExtensions category on NSString:
``` Objective-C
@interface NSString (NSStringPathExtensions)
@property (readonly, copy) NSArray *pathComponents;
- (NSString *)stringByAppendingPathComponent:(NSString *)str;
// ...
@end
```

It adds on the Objective-C NSString some properties and methods.

They will be exposed as static or instance methods and properties on the JavaScript constructor function and prototype object generated for NSString (see [Objective-C Classes](ObjC-Classes.md)).

You can use them from JavaScript:
``` JavaScript
var foo = NSString.stringWithString("foo");
var fooBar = foo.stringByAppendingPathComponent("bar");
var foo = foo.pathComponents;
```

> **NOTE:** Marshaling of strings will convert NSString to JavaScript string back and forth when they are passed or retrieved from native methods or functions, with the exception of NSString methods that are factory functions or initializers. [See detailed information in marshalling](../Marshalling.md).

Objective-C Categories can also implement Objective-C Protocols but this doesn't have special effect on the JavaScript objects beside the exposed functions and properties.