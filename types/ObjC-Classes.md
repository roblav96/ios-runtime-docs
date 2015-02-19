---
nav-title: "Objective-C Interfaces"
title: "Objective-C Interfaces"
description: "Describes how Objective-C Interfaces are exposed."
position: 0
---

# Objective-C Classes
The Objective-C classes are exposed as JavaScript classes. Each Objective-C class is presented by a pair of corresponding JavaScript constructor function and a prototype object. The methods declared in the Objective-C classes are exposed:
 - If static - on the JavaScript constructor function
 - If instance - on the JavaScript prototype object
For Objective-C properties, JavaScript properties are declared on the prototype object.

## Prototype Chain
The prototype chain of the JavaScript objects matches the inheritance chain of the represented Objective-C classes. For example:

``` Objective-C
@interface BaseInterface : NSObject
 + (void) baseStatic;
 - (void) baseInstance;
@end

@interface DerivedInterface : BaseInterface
 + (void) derivedStatic;
 - (void) derivedInstance;
@end
```

Will generate JavaScript object graph that behaves like:

``` JavaScript
var BaseInterface = function() { /* ... */}
Object.setPrototypeOf(BaseInterface, NSObject);
BaseInterface.baseStatic = function () { /* ... */ }
BaseInterface.prototype = Object.create(NSObject.prototype);
BaseInterface.prototype.baseInstance = function() { /* ... */ }

var DerivedInterface = function() { /* ... */ }
// Static methods are inherited
Object.setPrototypeOf(DerivedInterface, BaseInterface);
DerivedInterface.derivedStatic = function () { /* ... */ }

// Instance methods are inherited
DerivedInterface.prototype = Object.create(BaseInterface.prototype);
DerivedInterface.prototype.derivedInstance = function () { /* ... */ }

var derived = DerivedInterface.alloc().init();
// Will be true:
var isBase = derived instanceof BaseInterface;
var isNSObject = derived instanceof NSObject;
```

## Instantiating Objects

### Alloc Init or New
Objective-C instances are created using:
``` Objective-C
UIView* view1 = [[UIView alloc] init];
// Or with the short-cut
UIView* view2 = [UIView new];
```

Which is exposed as:
``` JavaScript
var view1 = UIView.alloc().init();
// Or with the short-cut
var view2 = UIView.new();
```

### New
The *new* keyword used with JavaScript constructor function for Objective-C class will try to locate an appropriate init method, and do an alloc().init(). However when a class has more complex initializers, it can be difficult to select one at runtime. So we would like to discourage you from using *new*, but still in some simple cases you can:

``` JavaScript
// Will call an UIView.alloc().init();
var view = new UIView();

// Will call an UIView.alloc().initWithFrame(...)
var dictionary = new UIView(CGRectMake(10, 10, 200, 100));
```

## Methods
When Objective-C methods are exposed in JavaScript, we remove the colons from their names, and then upper-case the letters following the removed colons.

For example:
``` Objective-C
@interface UIAlertView : UIView
- (instancetype)initWithTitle:(NSString *)title
                      message:(NSString *)message
                     delegate:(id)delegate
            cancelButtonTitle:(NSString *)cancelButtonTitle
            otherButtonTitles:(NSString *)otherButtonTitles,
            , ...;
@end
```
Will form the following JavaScript instance method:
``` JavaScript
var instance = UIAlertView.alloc();
instance.initWithTitleMessageDelegateCancelButtonTitleOtherButtonTitles("Title", "Message", null, "OK", null);
```

### Static Methods
Static methods are exposed in JavaScript as functions that call the underlying native methods. They are defined as properties on the constructor function. Since the constructor functions of derived classes have for prototype their base class constructor function, static methods are inherited.

For example:
``` Objective-C
@interface BaseInterface : NSObject
 + (void) baseStatic;
@end

@interface DerivedInterface : BaseInterface
 + (void) derivedStatic;
@end
```

You can call in JavaScript:
``` JavaScript
BaseInterface.baseStatic();

DerivedInterface.baseStatic();
DerivedInterface.derivedStatic();
```

### Instance Methods
Instance methods are exposed in JavaScript as functions that call the underlying native methods. They are defined as properties on the prototype object. Since the prototype objects of derived classes have for prototype their base class' prototype, instance methods are inherited.

For example:
``` Objective-C
@interface BaseInterface : NSObject
 + (void) baseInstance;
@end

@interface DerivedInterface : BaseInterface
 + (void) derivedInstance;
@end
```

You can call in JavaScript:
``` JavaScript
var baseInstance = BaseInterface.alloc().init();
baseInstance.baseInstance();

var derivedInstance = DerivedInterface.alloc().init();
DerivedInterface.baseInstance();
DerivedInterface.derivedInstance();
```

## Properties
Objective-C properties are exposed as JavaScript properties. For example consider the JavaScript objects generated for the following Objective-C interface:
``` Objective-C
@interface Derived : NSObject
@property (nonatomic, retain) NSString value;
@end
```

The prototype for the Derived class will have defined property "value" that when get or set on JavaScript instances of Derived will use the Objective-C property getter and setter:
``` JavaScript
var derived = Derived.alloc.init();
derived.value = "Hello World";
console.log(derived.value);
```

In Objective-C the getter methods generated by default have the name of the property and the setter methods have for name, the property name prefixed with "set". Because of the collisions the getter and setter methods for properties are shadowed and are not exposed as JavaScript functions.

## Extending
[You can subclass Objective-C classes in JavaScript.](../Extending.md)











