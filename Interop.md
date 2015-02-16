# Interop

## `Reference`

The parameter type is not limited to Objective-C types.

```javascript
var errorRef = new interop.Reference();
InterfaceDeclaration.methodWithOutParameter(errorRef);
var error = errorRef.value;
if (error) {
    var message = error.localizedDescription();
}
```

## `Pointer`

## `adopt`
Make a pointer instance "own" its memory.

## `alloc`

## `free`

## `sizeof`

## `handleof`

## Structures