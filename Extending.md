# Inheriting Native Types in JavaScript

The basic syntax for doing this is

```js
var constructorFunction = <TypeName>.extend({<Overrides and Exposed-to-native Methods Object>},[optional]{Native Mappings Object});

var myJSInheritedObject = new constructorFunction();
```

### The Overrides Object

Contains JavaScript overrides of native base class methods. Also, this object can be used to expose JavaScript functions as Objective-C, native methods of the inheritor class.

The object structure is:

```js
{<methodNameWithArgumentsNamesInCamelCase> : function () {} , .......}
```

`methodNameWithArgumentsNamesInCamelCase` is a result of one way, one to many transformation of native Objective-C method names. The `:` gets removed from the original Objective-C method name and the character immediately succeeding it gets capitalized. E.g. a projection of `collectionView:viewFor:supplementaryElementOfKind` should appear as `collectionViewViewForSupplementaryElementOfKind` in the Overrides Object. Note that `collectionView:viewFor:supplementaryElement:ofKind` will also appear as `collectionViewViewForSupplementaryElementOfKind` in the Overrides Object. So, currently it is impossible to expose and object that has both `collectionView:viewFor:supplementaryElementOfKind` and `collectionView:viewFor:supplementaryElement:ofKind`.

Calls to native base class methods in overrides are in the form:
```js
<BaseTypeName>.prototype.baseMethodNameWithArgumentsNamesInCamelCase.apply(this, arguments);
```
or
```js
this.super.baseMethodNameWithArgumentsNamesInCamelCase.apply(arguments);
```

To override Objective-C properties you have to use JavaScript getters/setters (ECMA 5). If the property isn't readonly, you have to override **both** the getter and the setter. Simple example:

```Objective-C
@property (getter=customGetter, setter=customSetter:) int property;
```

```js
var class = Foundation.MyClass.extend({
    get property() { return -this.super.property; },
    set property(x) { this.super.property = x * 2; },
});

var instance = class.alloc().init();
instance.property = 3;
console.log(instance.property); // -6
```

### The Native Mappings Object

This parameter is optional. If provided it should contain two properties:
* `name` - the name of the native class representing the JavaScript inheritor. Used to enable reflection scenarios.
* `exposedMethods` - allows explicitly defining native type encodings for the methods defined in the Overrides Object. Thus one can specify the exact native signature of a given JavaScript function. Type encoding are described [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html).
* `protocols` - an array of protocols that the new class will conform to.

> When exposing a new method to the Objective-C runtime (as opposed to overriding an existing method), its name in both the overrides object and exposedMethods object has to be the exact same string. This string is also used as the selector of the method in Objective-C.

Here is an example:
```js
NSObject.extend({
  "square:": function square(x) {
    return x * x;
  }
}, {
 name: "MyMath",
 exposedMethods: {
   "square:": "ii"
 },
 protocols: [ NSCopying ]
});
```
<del>
# Implementing Native Interfaces (Protocols) in JavaScript

Implementing a protocol is done also by calling `extend()`.

* If the native type implements a protocol its name should be added to the **protocols** array in the native mapping object.
* If you only want to implement a protocol then extend **NSObject** and continue as usual.

Here is an example:

```js
UICollectionViewController.extend({
    // other overrides and exposed methods

     collectionViewLayoutSizeForItemAtIndexPath: function(cv, cvl, ip) {
        ...
     }
}, {
    protocols: [ UICollectionViewDelegateFlowLayout ]
})
```
