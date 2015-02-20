---
nav-title: "Subclassing Objective-C"
title: "Subclassing Objective-C"
description: "How to extend Objective-C classes and implement protocol from JavaScript."
position: 0
---

# Subclassing Objective-C
For [Objective-C classes](../types/ObjC-Classes.md) we have JavaScript constructor functions and for [Objective-C protocols](../types/ObjC-Protocols.md) we have objects. They can be used to subclass an Objective-C class or implement Objective-C protocol from JavaScript.

### Subclassing an Objective-C Class
The constructor functions have a static method called **`extend`** used to declare Objective-C subclasses from JavaScript.

### Implementing Protocols
To implement a protocol use the **`extend`** method on an Objective-C class, the implemented protocols are provided in the extend arguments.

## Extend
The **`extend`** method has the following usage:

`var <DerivedClass> = <BaseClass>.extend(classMembers, [nativeSignature]);`

#### classMembers
The properties of the `classMembers` argument define the instance class members:
 * *functions* - define or override class methods
 * *properties* - define or override class properties

For more information how Objective-C method names are mapped to JavaScript property names check the ["Methods" section in "Objective-C Classes"](../types/ObjC-Classes.md#methods).

To override Objective-C properties you have to use JavaScript getters/setters (ECMA 5). If the property isn't read-only, you have to override both the getter and the setter.
 
#### nativeSignature
The `nativeSignature` argument is optional, it has the following properties:
 * **name** - optional, string with the derived class name
 * **protocols** - optional, array with the implemented protocols
 * **exposedMethods** - optional, dictionary with method names and `native method signature` objects
 
The `exposedMethods` property defines the Objective-C signatures of new methods, this is usually required from delegates and APIs that expect target with selector pairs. Methods that are overridden will infer their signatures from the base class or protocols they implement.

> **NOTE:** When exposing a new method to the Objective-C runtime (as opposed to overriding an existing method), its name in both the classMembers object and exposedMethods object has to be the exact same string. This string is also used as the selector of the method in Objective-C.

The `native method signature` object has two properties:
 * **returns** - required, type object
 * **params** - required, an array of type objects

The `type object` in general is one of the:
 * A constructor function, that identifies the Objective-C class
 * A primitive types in the `interop.types` object
 * In rare cases can be a reference type, struct type etc. described with the interop API

## Calling Base Methods
Calls to native base class methods in overrides are in the form:

`<BaseTypeName>.prototype.<MethodName>.apply(this, arguments);`

or

`this.super.<MethodName>.apply(arguments);`

Getting or setting properties using the base getters and setters is possible through the `super` property.

### Subclass Example
The following example subclasses the UIViewController:
```javascript
var MYViewController = UIViewController.extend({
	// Override an existing method from the base class.
	// We will obtain the method signature from the protocol.
    viewDidLoad: function () {
		// Call super using the prototype:
        UIViewController.prototype.viewDidLoad.apply(this, arguments);
		// or the super property:
		this.super.viewDidLoad();
		
		// Add UI to the view here...
    },
    shouldAutorotate: function () { return false; },
	
	// You can override existing properties
	get modalInPopover() { return this.super.modalInPopover; },
    set modalInPopover(x) { this.super.modalInPopover = x; },

	// Additional JavaScript instance methods or properties that are not accessible from Objective-C code.
	myMethod: function() { },
	
	get myProperty() { return true; },
    set myProperty(x) { },
}, {
    name: 'MYViewController'
});
```

### Protocol Implementation Example
The following example implements the UIApplicationDelegate protocol:
``` JavaScript
var MYAppDelegate = UIResponder.extend({
	// Implement a method from UIApplicationDelegate.
	// We will obtain the method signature from the protocol.
    applicationDidFinishLaunchingWithOptions: function () {
        this._window = new UIWindow(UIScreen.mainScreen().bounds);
        this._window.rootViewController = MYViewController.alloc().init();
        this._window.makeKeyAndVisible();
        return true;
    }
}, {
	// The name for the registered Objective-C class.
    name: "MYAppDelegate",
	// Declare that the native Objective-C class will implement the UIApplicationDelegate Objective-C protocol.
    protocols: [UIApplicationDelegate]
});
```

### Delegate Example
The following example shows how you can create a new method accessible from Objective-C APIs, that is declared in JavaScript:
``` JavaScript
var MYViewController = UIViewController.extend({
	viewDidLoad: function () {
		// ...
		var aboutButton = UIButton.buttonWithType(UIButtonType.UIButtonTypeRoundedRect);
		// Pass this target and the aboutTap selector for touch up callback.
		aboutButton.addTargetActionForControlEvents(this, "aboutTap", UIControlEvents.UIControlEventTouchUpInside);
		// ...
    },
	// The aboutTap is a JavaScript method that will be accessible from Objective-C.
	aboutTap: function(sender) {
        var alertWindow = new UIAlertView();
        alertWindow.title = "About";
        alertWindow.addButtonWithTitle("OK");
        alertWindow.show();
    },
}, {
    name: "MYViewController",
    exposedMethods: {
		// Declare the signature of the aboutTap. We can not infer it, since it is not inherited from base class or protocol.
        aboutTap: { returns: interop.types.void, params: [ UIControl ] }
    }
});
```

