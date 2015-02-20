NativeScript uses the JavaScriptCore (JSC) JavaScript engine. Since iOS 7, the JSC comes with a built-in JavaScript-Objective-C bridge, which makes it a perfect fit for NativeScript.

Let's see what NativeScript application workflow and lifecycle are. Every NativeScript iOS app mimics a regular Objective-C application using JavaScript as a programming language. If you have experience with iOS and Objective-C development, you should feel comfortable and build your first app in minutes since the programming model and application lifecycle events are the same. Keep in mind, though, that NativeScript targets JavaScript developers as well, so we made some design choices that make NativeScript more "JavaScript-friendly".

During the startup process, NativeScript will initialize the JSC and execute a `bootstrap.js` file, which serves as an entry point for your JavaScript code.

Once the JSC passes the control flow to your JavaScript, things become much more interesting. After all, you would want to do all the fancy things in JavaScript that you can do in Objective-C. Let's start with the examples. We will now show how Objective-C code is translated to its NativeScript counterpart.

## Your First Hello World App

This code sample may look a little strange at first, but it contains most of the syntax you will have to use and will give you a high-level understanding of what NativeScript looks like. We will explain every specific of the syntax in detail in short, so don't worry about it now.

If you are familiar with iOS programming, you will know that xCode generates something like the following for a blank application:
```objective-c
@interface TNSAppDelegate : UIResponder <UIApplicationDelegate>
@property (strong, nonatomic) UIWindow *window;
@end

@implementation TelerikAppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    self.window.backgroundColor = [UIColor redColor];
    [self.window makeKeyAndVisible];
    return YES;
}
@end
```

Here is the equivalent application in NativeScript:
```javascript
UIKit.UIResponder.extends({}, {
    name: "TNSAppDelegate"
}).implements({
    protocol: UIKit.UIApplicationDelegate,
    implementation: {
        applicationDidFinishLaunchingWithOptions: function(application, launchOptions) {
            this._window = new UIKit.UIWindow(UIKit.UIScreen.mainScreen().bounds);
            this._window.backgroundColor = UIKit.UIColor.redColor();
            this._window.makeKeyAndVisible();
            return true;
        }
    }
});
```

In this example (above), we are creating a blank, red window. This is accomplished by inheriting the `UIResponder` class using the `extends` method and implementing the `UIApplicationDelegate` protocol using the `implements` method. The `applicationDidFinishLaunchingWithOptions` callback is called when the application has successfully finished launching.

> **IMPORTANT:** The class must be named `TNSAppDelegate` in order to be recognized as application entry point by NativeScript.

Now that you've seen the big picture, let's see everything we just did in detail.

## Using the Objective-C API

The Objective-C language builds up on C and adds object-oriented features. These include classes and protocols which can have **properties, instance and static methods**.

Let's look again at a simple code sample:
```objective-c
@interface InterfaceDeclaration : NSObject <ProtocolDeclaration>
@property int property;
+ (int)staticMethod:(int)param1 withParameter:(int)param2;
- (int)instanceMethod:(int)param1 withParameter:(int)param2;
@end
```

Using it in Objective-C:
```objective-c
InterfaceDeclaration *instance = [[InterfaceDeclaration alloc] init];
instance.property = 3; int propertyValue = instance.property;
int staticMethodValue = [InterfaceDeclaration staticMethod:4 withParameter:5];
int instanceMethodValue = [instance instanceMethod:6 withParameter:7];
```

And the same code written in NativeScript:
```javascript
var instance = new Foundation.InterfaceDeclaration();
instance.property = 3; var propertyValue = instance.property;
var staticMethodValue = Foundation.InterfaceDeclaration.staticMethodWithParameter(4, 5);
var instanceMethodValue = instance.instanceMethodWithParameter(6, 7);
```

Similarly we use the same `<Framework>.<Member>` convention to access classes and protocols.

###Constructors
Next we are **creating an instance** of the class using `new`. In this scenario NativeScript is smart enough to resolve the correct constructor for you based on the number and type of the arguments. In cases of ambiguity or you wish the code to be more explicit you could call any constructor explicitly by name using syntax like `Foundation.InterfaceDeclaration.init()`.

###Properties
Objective-C **properties** are used as JavaScript properties. We are not using the name of the getters/setters, just the name of the property.

###Selectors
Objective-C uses **message (selector) passing** to object instances. In this case a sample selector looks like `staticMethod:withParameter:`. To translate this to a JavaScript method name, remove each of the `:` and capitalize the character immediately succeeding it. The result in this case is `staticMethodWithParameter`.


## Inheriting Objective-C classes
We saw how we can use the framework classes. Now let's see how you can **define your own class**.

When we want to inherit a Objective-C class we call it's `extends` function. The `extends` function returns a Javascript constructor function that will return an instance of our inheritor object when called. The basic syntax for doing this is:
```javascript
var <Constructor> = <Framework>.<Class>.extends(
    <Methods implementation>,
    [optional]<Methods metadata>
);
var <Instance> = new <Constructor>();
```


### Simple inheritance
Let's see a simple example, where we just override the `description` method of `NSObject` and return the `super` call result. In Objective-C you would do:
```objective-c
@implementation JSObject : NSObject
- (NSString *)description {
    return [super description];
}
@end

JSObject *instance = [[JSObject  alloc] init];
NSLog([instance description]);
```

And again the same in NativeScript:
```javascript
var JSObject = Foundation.NSObject.extends({
    description: function() {
        return this.super.description();
    }
});

var instance = new JSObject(); // Again same as JSObject.init()
Foundation.NSLog(instance.description());
```

### Adding native visible methods to the inheritor using `extends`
The **first parameter of the extends function** is an object contaning all the methods of the inheritor object. There are three type of methods, which can be contained in this object - base class overrides, native visible methods and pure JavaScript methods. Again we use the same selector to JavaScript method name conversion.

The difference between native visible/pure Javascript methods is the that later are only accessible in your JavaScript code. Should you want the method to be visible and callable from the native libraries, you should provide a **second** parameter to `extends`. This parameter is optional, but it can provide the needed additional metadata. It can contain two properties:

* **name** - the name of the native class representing the JavaScript inheritor. Used to enable reflection scenarios.
* **exposedMethods** - the exact native method signatures of a given JavaScript function.

Let's see this in context:

```javascript
var JSObject = Foundation.NSObject.extends({
    selectorWithXAndY: function(x, y) {
        Foundation.NSLog("custom selector called");
    }
}, {
    name: "JSObject",
    exposedMethods: {
        "selectorWithX:andY:": "v@:ii"
    }
});

var instance = new JSObject();
instance.selectorWithXAndY(7, 8);
```

Now it is possible to do the following in Objective C:

```objective-c
id instance = [[NSClassFromString("JSObject") alloc] init];
[instance selectorWithX:7 andY:8];
```

Let's see how the [type-encoding](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html) of the signature is built. The first argument is the return type. The second one is allways `@` (`id`), the third one is allways `:` (`SEL`) and following are the parameter types. Note that in the `exposedMethods` object we are using the selector name and in the methods object we are using the JavaScript name.

### Overriding properties

To override Objective-C properties you have to use JavaScript getters/setters. If the property isn't `readonly`, you have to override **both** the getter and the setter.

Here is a simple code sample. Given the property:
```objective-c
@property (getter=customGetter, setter=customSetter:) int propertyDeclaration;
```

you have to use:
```javascript
var Constructor = Foundation.InterfaceDeclaration.extends({
    get propertyDeclaration() { return -this.super.propertyDeclaration; },
    set propertyDeclaration(x) { this.super.propertyDeclaration = x * 2; },
});
```

The native getter/setter name again are irrelevant.

### Some caveats
* When overriding constructors, you shouldn't call the super constructor - it is called implicitly before the override.
* The first parameter to `extends` is set as `prototype` to the instances created by the constructor. You shouldn't reuse it for other `extends` calls.
* Certain methods can't be overridden [TODO: Link to methods that can not be overridden]


## Implementing Objective-C protocols
The syntax for implementing protocols is quite similar - you call the `implements` method on the protocol you wish to implement or on the result constructor function of an `extends` call. So you can extend once to inherit a class and then implement multiple protocols.

You can implement only some methods of the protocol. If a not implemented method is called an exception will be raised at runtime. You can implement only some or none of the optional methods.

Let's see code sample for both scenarios.

### Calling `implements` directly on the protocol
```javascript
var JSObject = Foundation.ProtocolDeclaration1.implements({
    method1: function() {
        Foundation.NSLog('method1 called');
    },
    method2: function() {
        Foundation.NSLog('method2 called');
    }
});
var instance = new JSObject(); // Or again JSObject.init();
instance.method1();
```
This is just a shorthand for inheriting from `NSObject` and implementing `ProtocolDeclaration`.

### Using `extends` and then multiple `implements`
```javascript
var JSObject = Foundation.NSObject.extends({
    baseMethod: function() {
        Foundation.NSLog("baseMethod called");
    }
}).implements({
    protocol: Foundation.ProtocolDeclaration1,
    implementation: {
        method1: function() {
            Foundation.NSLog('method1 called');
        },
        method2: function() {
            Foundation.NSLog('method2 called');
        }
    }
}).implements({
    protocol: Foundation.ProtocolDeclaration2,
    implementation: {
        method3: function() {
            Foundation.NSLog('method3 called');
        }
    }
});
var instance = new JSObject(); // Or again JSObject.init();
instance.method1();
```

Here our class inherits a single class and implements two protocols.


## Using the C API

The C language is fairly simple. In general you have **functions, structs/unions, enumerations and constants**. Pointers and function pointers have a separate section and will be explained later. We will remind you how to use them in C and how the same code translates to NativeScript:

Let's say that this is our API header in the `Foundation` framework:
```c
// Function
int functionDeclaration(int param1, int param2);

// Struct
struct StructDeclaration { int field1; int field2; };

// Union
union UnionDeclaration { int field1; int field2; };

// Enum
enum EnumDeclaration { EnumMember1, EnumMember2, EnumMember3 };

// Constant
extern const int ConstantDeclaration;
```

If you are using plain C, you will have to write:
```c
int resultValue = functionDeclaration(1, 2);

struct StructDeclaration structValue = {
    .field1 = 3,
    .field2 = 4,
};

union UnionDeclaration unionValue = {
    .field1 = 5
};

enum EnumDeclaration enumValue = EnumMember1;

int constantValue = ConstantDeclaration;
```

And here is the NativeScript code:
```javascript
var resultValue = Foundation.functionDeclaration(1, 2);

var structValue = Foundation.StructDeclaration.create();
structValue.field1 = 3;
structValue.field2 = 4;

var unionValue = Foundation.UnionDeclaration.create();
unionValue.field1 = 5;

var enumValue = Foundation.EnumDeclaration.EnumMember1;

var constantValue = Foundation.ConstantDeclaration;
```

There are a few differences. Each global declaration is exposed as a property on the framework object it's contained in. JavaScript uses dynamic typing and each value will be translated to the respective type.

Structures are allocated on the heap, but you don't need to explicitly free their memory once you no longer need them. The memory is freed when the JavaScript object is garbage-collected. More complex scenarios like arrays in structs and nested structs are just as well supported and will work as expected.

> **NOTE:** Anonymous enumerations are not supported, but you can pass their integer value as a plain JavaScript number.
