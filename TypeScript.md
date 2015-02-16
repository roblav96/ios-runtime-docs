# TypeScript

## Inheritance

You can use TypeScript to inherit from native classes.

```typescript
// A native class with the name "JSObject" will be registered, so it should be unique
class JSObject extends NSObject {
    public encodeWithCoder(aCoder) { /* ... */ }
    
    public initWithCoder(aDecoder) { /* ... */ }
    
    public 'selectorWithX:andY:'(x, y) { /* ... */ }

    // An array of protocols to be implemented by the native class
    public static ObjCProtocols = [NSCoding];

    // A selector will be exposed so it can be called from native.
    public static ObjCExposedMethods = {
        'selectorWithX:andY:': 'v@@'
    };
}
```

There should be no TypeScript constructor, because it will not be executed. Instead override one of the `init` methods.

You shouldn't extend an already extended class.