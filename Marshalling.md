---
nav-title: "Marshalling"
title: "Marshalling "
description: "Describes how JavaScript objects are marshalled to Objective-C and back."
position: 0
---

# Marshalling

## Native to JavaScript

| Native             | JavaScript                                  |
| ------             | ----------                                  |
| `id`               | wrapper object                              |
| `instancetype`     | wrapper object                              |
| `Class`            | [constructor object](types/ObjC-Classes.md) |
| `Protocol*`        | [protocol object](types/ObjC-Protocols.md)  |
| `SEL`              | string                                      |
| `unichar`          | single character string                     |
| `NSString*`        | string/wrapper object*                      |
| `NSMutableString*` | wrapper object                              |
| `NSDate*`          | Date/wrapper object*                        |
| `NSNumber*`        | number                                      |
| `int8_t`           | number                                      |
| `uint8_t`          | number                                      |
| `int16_t`          | number                                      |
| `uint16_t`         | number                                      |
| `int32_t`          | number                                      |
| `uint32_t`         | number                                      |
| `int64_t`          | number*                                     |
| `uint64_t`         | number*                                     |
| `float`            | number                                      |
| `double`           | number                                      |
| `void`             | undefined                                   |
| `nil`              | null                                        |
| `Nil`              | null                                        |
| `NULL`             | null                                        |
| `[NSNull null]`    | null/wrapper object*                        |
| `true`/`false`     | boolean                                     |
| `YES`/`NO`         | boolean                                     |
| `@YES`/`@NO`       | boolean                                     |
| `CFTypeRef`        | [Pointer](types/C-Pointers)                 |
| `void*`            | [Pointer](types/C-Pointers)                 |
| `<type>*`          | [Reference](Interop.md)                     |
| `<type>[]`         | [Reference](Interop.md)                     |
| Function pointer   | [function](types/C-Function-Pointers.md)    |
| Objective-C block  | [function](types/ObjC-Blocks)               |
| Structure          | [wrapper object](types/C-Structures.md)     |

### Marshalling `instancetype` type
When `NSString*`, `NSDate*`, `NSNull*` are marshalled from native to JavaScript they are converted to JavaScript `string`, `Date` or `null`.

But if you use the constructor or call the factory method for these types, a wrapper is returned and you can call native methods on it:

```javascript
var nsString1 = NSString.alloc().initWithString("test"); // NSString* wrapper
var nsString2 = NSString.stringWithString("test"); // NSString* wrapper

var nsDate1 = NSDate.alloc().initWithTimeIntervalSince1970(1E9); // NSDate* wrapper
var nsDate2 = NSDate.dateWithTimeIntervalSince1970(1E9); // NSDate* wrapper

var nsNull = NSNull.null(); // NSNull* wrapper
```

However, if we create a `NSArray*` from the values and get them back, we will get the JavaScript primitives:
```javascript
var nsArray = NSArray.alloc().initWithArray([nsString1, nsDate1, nsNull]);
console.log(nsArray[0]); // JavaScript string: "test"
console.log(nsArray[1]); // JavaScript date: "Sun Sep 09 2001 04:46:40 GMT+0300 (EEST)"
console.log(nsArray[2]); // JavaScript null
```

### Object Subscripting
Objects such as `NSMutableArray*`, `PHFetchResult*` and other which respond to `objectAtIndexedSubscript:`/`setObject:atIndexedSubscript:` selectors can use indexers to get/set value at index:

```javascript
var nsArray = NSMutableArray.arrayWithArray([5, 7, 9]);

nsArray.setObjectAtIndexedSubscript(-5, 0);
console.log(nsArray.objectAtIndexedSubscript(0)); // -5

nsArray[1] = -7;
console.log(nsArray[1]); // -7
```

### `CFTypeRef` Marshalling
Objects that inherit from [`CFTypeRef`](https://developer.apple.com/library/ios/documentation/CoreFoundation/Reference/CFTypeRef/index.html) are marshalled as plain [Pointers](types/C-Pointers.md) and no automatic memory managment is done. You should call `CFRetain`/`CFRelease` to track the lifetime of these objects:

```javascript
var bag = CFBagCreate(kCFAllocatorDefault, values, count, null);
// ...
CFRelease(bag);
```

## Limitations

 * `int64_t`/`uint64_t` precision - TODO: Link to common repo
