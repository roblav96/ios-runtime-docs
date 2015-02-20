# Third-Party

## Getting Started with CocoaPods

Install CocoaPods:
```shell
sudo gem install cocoapods
```

Create a Podfile:
```shell
pod init
```

And reference some pods, in our example:
```shell
pod "AFNetworking"
```

Install the pods:
```shell
pod install
```

Add new header file `metadata.h` and import the pod headers in it, in our example:
```objective-c
#import <UIKit/UIKit.h>

#import "AFNetworking.h"
#import "UIKit+AFNetworking.h"
```

Then build metadata for it, in our example execute in the root of the repo:
```
grunt metadata \
	-header Examples/ExternalLibraries-App/metadata.h \
	-output Examples/ExternalLibraries-App/ \
	-cflags=" \
		-I`pwd`/Examples/ExternalLibraries-App/Pods/Headers/AFNetworking/ \
	"
```
This will generate a reference folder with `metadata.bin` inside.
You should add it as `metadata.bin` bundle resource in your app.

You have to make sure to put -ObjC as additional linker flag, otherwise the linker may strip you Objective-C classes.

This should expose in the JavaScript runtime Objective-C classes.
You cannot use functions, constants and C API in general yet.
