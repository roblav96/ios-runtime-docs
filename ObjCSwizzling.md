# Objective-C Swizzling

You can swizzle a static method, instance method or a property of a native class.

```javascript
var nativeStaticMethod = TNSSwizzleKlass.staticMethod;
TNSSwizzleKlass.staticMethod = function (x) {
    return 2 * nativeStaticMethod.apply(this, arguments);
};

var nativeInstanceMethod = TNSSwizzleKlass.prototype.instanceMethod;
TNSSwizzleKlass.prototype.instanceMethod = function (x) {
    return 2 * nativeInstanceMethod.apply(this, arguments);
};

var nativeProperty = Object.getOwnPropertyDescriptor(TNSSwizzleKlass.prototype, 'aProperty');
Object.defineProperty(TNSSwizzleKlass.prototype, 'aProperty', {
    get: function () {
        return 2 * nativeProperty.get.call(this);
    },
    set: function (x) {
        nativeProperty.set.call(this, 2 * x);
    }
});
```

You can swizzle an already swizzled method or property:

```javascript
var nativeStaticMethod = TNSSwizzleKlass.staticMethod;
TNSSwizzleKlass.staticMethod = function (x) {
    return 2 * nativeStaticMethod.apply(this, arguments);
};

var swizzledMethod = TNSSwizzleKlass.staticMethod;
TNSSwizzleKlass.staticMethod = function (x) {
    return 2 * swizzledMethod.apply(this, arguments);
};
```

Use method swizzling as a [last resort](http://nshipster.com/method-swizzling/#considerations).
