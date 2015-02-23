## Using plain C pointers
In NativeScript you can allocate a block of memory and pass it where a pointer is expected. You do this by calling the function: `NativePointer.create(<Type>, <Count>)`. You will recieve an object, with which you can do pointer arithmetic using array-like syntax in JavaScript.

The **first parameter** of the function can be either a `PrimitiveType.<Type>` or a `<Framework>.<Struct>` object. Supported primitive types are: `VOID`, `BOOL`, `UNSIGNED_CHAR`, `UNSIGNED_SHORT`, `UNSIGNED_INT`, `UNSIGNED_LONG`, `UNSIGNED_LONG_LONG`, `CHAR`, `SHORT`, `INT`, `LONG`, `LONG_LONG`, `FLOAT` and `DOUBLE`. The **second parameter** is the number of elements to be allocated.

Don't forget to `delete` the memory, when you have finished using it.

Lets see a simple example. Given the function:
```c
void functionWithIntPointer(int *x);
```
you can use it as follows:
```javascript
var arguments = NativePointer.create(PrimitiveType.INT, 2); // int *arguments = malloc(2 * sizeof(int));
arguments[0] = 1; // *(arguments + 0) = 1;
arguments[1] = 2; // *(arguments + 1) = 2;
functionWithIntPointer(arguments);
arguments.delete(); // free(arguments);
```

You should use JavaScript `null` as `NULL` pointer.

## Using Objective-C out parameters
We have implemented double pointers for Objective C Types only since these are widely used as out parameters. Many methods in the Objective-C API will have an `NSError **` as an out parameter at the end to give some more detail if something went wrong:
```objective-c
+ (void)methodWithOutParameter:(NSError **)outError;
```

The syntax to use such methods and access the returned value is the following:
```javascript
var errorRef = RefValue.create();
Foundation.InterfaceDeclaration.methodWithOutParameter(errorRef);
var error = errorRef.value;
if (error) {
    var message = error.localizedDescription();
}
```

Note that you shouldn't make any `retain/release` calls explicitly.

## Using function pointers
In order to use a plain JavaScript function as a function pointer you must first make the call to `NativePointer.create(<Function>)`. It will modify the given function and return it. You have to call `delete` when you or the API you have passed it to have finished using it.

The signature of the provided function must mach that of the function pointer. It is recommended to provide an anonymous function and not to reuse it somewhere else.

Lets see an example, where we pass a function pointer as parameter:
```objective-c
void functionWithFunctionPointer(int (*f)(int));
```
```javascript
var f = NativePointer.create(function(x) { return x * x; });
Foundation.functionWithFunctionPointer(f);
f.delete();
```


## Using blocks
You can pass plain JavaScript functions to types that expect a block. Again the signature of the block must mach that of the provided function. There is no need to do anything special. You will recieve a JavaScript function if a method returns block.

## Using `long long`/`unsigned long long` types
Values that are representable in JavaScript without loss are marshalled as primitive JavaScript numbers. These values ar in the range from `-2^53` to `2^53`. The values that exceed this range are wrapped in objects. You can use the `value` property to see a string representation of the number and you can pass this object back to the native API where a `long long`/`unsigned long long` is expected. You can't create these type of objects in JavaScript.

## Handling Objective-C exceptions
Objective-C exceptions are caught from NativeScript and rethrown in JavaScript. You can see the native exception instance in the `exception` property.
```javascript
try {
    Foundation.InterfaceDeclaration.methodWhichThrowsNativeException()
} catch(e) {
    var description = e.exception.description();
    var callStackSymbols = e.exception.callStackSymbols().description();
}
```

## Checking if new iOS features are available
Newer versions of iOS contain some new classes, functions or constants. You can check if certain new members are available at runtime by using for example:

```javascript
if (<Framework>.<Member>) {
    // We are using a compatible iOS version. Use this like a normal class.
} else {
    // We are on and older iOS version. This is not available.
}
```
