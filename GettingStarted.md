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

## Splitting your application in multiple modules
Once you learn the basics of NativeScript you would probably want to write a larger app than a simple hello-world-like app. To be more productive you would probably want to structure your app into modules. NativeScript implements the [CommonJS modules](http://wiki.commonjs.org/wiki/Modules/1.1).

In most applications your folder structure should look something like:

    |- app/
    |--- bootstrap.js
    |--- module/
    |----- index.js
    |----- module_utils.js
    |- tns_modules/
    |--- camera/
    |----- index.js

The main starting point of your app is `bootstrap.js`. The `app` folder is where your application files are. The `tns_modules` is the folder where NativeScript Code Modules are. You can require additional files using `require("modulePath")`. The resolve algorithm is as follows:
* Checks for `app/modulePath`
* Checks for `app/modulePath/index.js`
* Checks for `tns_modules/modulePath`
* Checks for `tns_modules/modulePath/index.js` and loads it if exists
* Otherwise it fails

When requesting a module several times the result is cached. Circular references are not recommended, but supported.
Additionally you have the `global`, `module` and `exports` variables.

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
