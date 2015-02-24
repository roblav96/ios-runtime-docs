---
nav-title: "C Enums"
title: "C Enums"
description: "Describes how enumerated types are exposed."
position: 0
---

# Enums

Enumeration types are exposed as objects with the name of the `enum` and they have properties with read-only members.

For example, given the following definitions:
```objective-c
enum MFMailComposeResult {
    MFMailComposeResultCancelled,
    MFMailComposeResultSaved,
    MFMailComposeResultSent,
    MFMailComposeResultFailed
};

typedef NS_ENUM(NSInteger, NSFormattingUnitStyle) {
    NSFormattingUnitStyleShort = 1,
    NSFormattingUnitStyleMedium,
    NSFormattingUnitStyleLong
};

typedef NS_OPTIONS(NSUInteger, NSLinguisticTaggerOptions) {
    NSLinguisticTaggerOmitWords         = 1 << 0,
    NSLinguisticTaggerOmitPunctuation   = 1 << 1,
    NSLinguisticTaggerOmitWhitespace    = 1 << 2,
    NSLinguisticTaggerOmitOther         = 1 << 3,
    NSLinguisticTaggerJoinNames         = 1 << 4
};
```
You can use them from JavaScript in the following way:
```javascript
console.log(MFMailComposeResult.MFMailComposeResultSent); // 2
console.log(NSFormattingUnitStyle.NSFormattingUnitStyleMedium); // 2
console.log(NSLinguisticTaggerOptions.NSLinguisticTaggerOmitWhitespace); // 4
```

You can also go from a numeric value to the name of that value in the enum:
```javascript
console.log(MFMailComposeResult[2]); // "MFMailComposeResultSent"
```

Anonymous enumeration types have their members as global constants. For example:
```objective-c
enum {
    NSASCIIStringEncoding = 1,
    NSNEXTSTEPStringEncoding = 2,
    NSJapaneseEUCStringEncoding = 3,
    NSUTF8StringEncoding = 4,
    // ...
};
```
```javascript
console.log(NSUTF8StringEncoding); // 4
```

> **NOTE:** Values that fall out of the `int32_t` range are not supported. See [marshalling](Marshalling.md) for details.
