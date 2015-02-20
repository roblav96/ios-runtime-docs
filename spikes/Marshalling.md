# Marshalling

## Native to JavaScript

| Native                                    | JavaScript                                 |
| ------------------------                  | ----------                                 |
| `id`                                      | wrapper object                             |
| `CFTypeRef`                               | wrapper object                             |
| `Class`                                   | constructor object                         |
| `Protocol *`                              | protocol object                            |
| `SEL`                                     | string                                     |
| `NSString *`                              | string                                     |
| `NSString *` (From static factory method) | wrapper object                             |
| `NSArray *`                               | wrapper object with getter/setter by index |
| `NSNumber *`                              | number                                     |
| `char`                                    | number                                     |
| `unsigned char`                           | number                                     |
| `short`                                   | number                                     |
| `unsigned short`                          | number                                     |
| `int`                                     | number                                     |
| `unsigned int`                            | number                                     |
| `long`                                    | number                                     |
| `unsigned long`                           | number                                     |
| `long long`                               | number                                     |
| `unsigned long long`                      | number                                     |
| `float`                                   | number                                     |
| `double`                                  | number                                     |
| C structure                               | wrapper object                             |
| C array                                   | array                                      |
| `type *`                                  | Reference                                  |
| `void *`                                  | Pointer                                    |
| `NSBlock`                                 | function object                            |
| Function pointer                          | function object                            |
| `void`                                    | undefined                                  |
| `nil`/`Nil`                               | null                                       |
| `[NSNull null]`                           | null                                       |
| `__NSCFBoolean` (`@YES`/`@NO`)            | boolean                                    |
| `bool` (`true`/`false`)                   | boolean                                    |
| `_Bool` (`true`/`false`)                  | boolean                                    |
| `BOOL` (`YES`/`NO`)                       | boolean                                    |
| `unichar`                                 | string                                     |
