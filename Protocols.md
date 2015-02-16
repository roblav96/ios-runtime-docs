# Objective-C Protocols

There is a global object for each Objective-C protocol. It's name is the same as the native protocol. In case of conflicts with other types, the name has the `Protocol` suffix.

```JavaScript
var klass = NSObject;
var protocol = NSObjectProtocol;
```

Example:

```JavaScript
var protocol = NSProtocolFromString('UIApplicationDelegate');
console.log(NSStringFromProtocol(protocol)); // 'UIApplicationDelegate'
expect(protocol).toBe(UIApplicationDelegate);
```
