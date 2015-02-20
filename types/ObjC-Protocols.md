---
nav-title: "Objective-C Protocols"
title: "Objective-C Protocols"
description: "Describes how Objective-C protocols are exposed."
position: 0
---

#Objective-C Protocols
Objective-C protocols describe an API Objective-C classes may implement. JavaScript as dynamic language does not have a matching counterpart. For every Objective-C Protocol we expose a JavaScript object that identifies the protocol. These objects can be used in the extension API to create derived Objective-C classes that implement the protocols.

For example given the Objective-C classes:
``` Objective-C
@protocol MyProtocol
 - helloWorld;
@end
@interface MyClass : NSObject <MyProtocol>
@end
```

Will be exposed as:
``` JavaScript
// Will log 'true'
console.log(MyClass.conformsToProtocol(MyProtocol));

// Will log 'true'
var instance = MyClass.alloc().init();
console.log(instance.conformsToProtocol(MyProtocol));
```
