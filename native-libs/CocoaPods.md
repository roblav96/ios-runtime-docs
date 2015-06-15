---
nav-title: "CocoaPods"
title: "Using CocoaPods"
description: "How to install and use CocoaPods with NativeScript for iOS."
position: 1
---

# Using CocoaPods

#### Create CLI Project
To start, create a project, add the iOS platform, prepare the iOS platform. The CLI would generate an Xcode project for you.
```bash
$ tns create MYCocoaPods
$ cd MYCocoaPods
$ tns platform add ios
$ tns prepare ios
```
You will have Xcode project in `platforms/ios/MYCocoaPods.xcodeproj`.

#### Install CocoaPods
You will need to install cocoa pods. If you haven't yet, you can do so by:
```
$ sudo gem install cocoapods
```
This article is written with 0.37.1, you can check your current version using:
```
$ pod --version
```
To update CocoaPods just install the gem again with `sudo gem install cocoapods`.

#### Add Podfile with Dependencies
Create a Podfile as sibling to the `platforms/ios/MYCocoaPods.xcodeproj` and add the dependencies. We will use the [Google Maps SDK](https://developers.google.com/maps/documentation/ios/start) for the example:
```
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.1'
pod 'GoogleMaps'
```
Then install the pod dependencies. This should be executed in `platform/ios`:
```
$ pod install
```
It will augment the MYCocoaPods.xcodeproj and create a workspace with the same name.

> **NOTE:** From that point on you will not be able to run the xcodeproj alone, (nor the CLI since it too works with the xcodeproj) you will have to run your project from the workspace. The workflow would be to open the MYCocoaPods.xcworkspace in Xcode, then execute `$ tns prepare ios` in terminal and run the app from Xcode. We will implement support for workspaces in the CLI for v1.2.0.

#### Use the Google Maps SDK
We will set up the following app from our Hello World template:

![CocoaPods-GoogleMaps.png](CocoaPods-GoogleMaps.png)

To actually use the Google Maps you will have to provide API key in "applicationDidFinishLaunchingWithOptions". The cross platform UI modules expose callback, that is invoked in our AppDelegate. There you can register the key:
``` JavaScript
// app.js
var application = require("application");

application.onLaunch = function() {
    console.log('Providing GoogleMap API key...');
    // NOTE: Use the Google documentation to obtain API key: https://developers.google.com/maps/documentation/ios/start
    GMSServices.provideAPIKey("***************************************");
}

application.mainModule = "main-page";
application.cssFile = "./app.css";
application.start();
```

And then to place a map in the UI:
``` XML
<!-- main-page.js -->
<Page xmlns="http://www.nativescript.org/tns.xsd" loaded="pageLoaded">
  <GridLayout rows="auto, auto, auto, *">
    <Label row="0" text="Tap the button" cssClass="title"/>
    <Button row="1" text="TAP" tap="{{ tapAction }}" />
    <Label row="2" text="{{ message }}" cssClass="message" textWrap="true"/>
    <Placeholder row="3" creatingView="createMapView" />
  </GridLayout>
</Page>
```

And the code behind to initialize the map placeholder:
``` JavaScript
var vmModule = require("./main-view-model");
function pageLoaded(args) {
    var page = args.object;
    page.bindingContext = vmModule.mainViewModel;
}
exports.pageLoaded = pageLoaded;

var camera;
var mapView;
var marker;

function createMapView(args) {
    console.log("Creating map view...");
    camera = GMSCameraPosition.cameraWithLatitudeLongitudeZoom(-33.86, 151.20, 6);
    mapView = GMSMapView.mapWithFrameCamera(CGRectZero, camera);

    console.log("Setting a marker...");
    marker = GMSMarker.alloc().init();
    // Note that in-line functions such as CLLocationCoordinate2DMake are not exported.
    marker.position = { latitude: -33.86, longitude: 151.20 }
    marker.title = "Sydney";
    marker.snippet = "Australia";
    marker.map = mapView;

    console.log("Displaying map...");
    args.view = mapView;
}
exports.createMapView = createMapView;
```

#### CocoaPods Module Maps OR use_frameworks!
We generate metadata from Objective-C headers. The APIs are exposed in JavaScript using algorithm similar to how swift exposes Objective-C. In the process we need, the relatively new, clang modules declarations for Objective-C. They are declared using module map files.

[For more information about module maps check here.](http://clang.llvm.org/docs/Modules.html#umbrella-directory-declaration).

##### Use Frameworks
[CocoaPods now support Swift and dynamic frameworks](http://blog.cocoapods.org/CocoaPods-0.36/).
```
platform :ios, '8.0'
use_frameworks!

pod 'iCarousel'
pod 'AFNetworking'
```

Once you install the pods with the `use_frameworks!` enabled the pods will be packed in dynamic frameworks. The good part is that each pod will have its `module.modulemap` declared so you would be good to go with no extra manual setup. The down-side is these dynamic frameworks are supported in iOS 8+. While the static version, with manual module maps, can be used in iOS 7.

##### Module Maps
The GoogleMaps SDK had nicely done module map placed in `Pods/GoogleMaps/Frameworks/GoogleMaps.framework/Modules/module.modulemap`. It is there even if you do not `use_frameworks!`. For some CocoaPods however, you will have to provide module maps by hand.

Let's consider the following CocoaPod Podfile:
```
platform :ios, '8.0'

pod 'iCarousel'
pod 'AFNetworking'
```

Installing it with `pod install` will pull the two libraries. They do not have module maps (yet). If you want to use them you will have to add the following module maps in the file `platforms/ios/module.modulemap`:
```
module AFNetworking {
  umbrella "Pods/Headers/Public/AFNetworking"
  export *
  module * { export * }
}

module iCarousel {
  umbrella header "Pods/Headers/Public/iCarousel/iCarousel.h"
  export *
  module * { export * }
}
```

This should be enough to enable the two pods in your project. Note that the AFNetworking.h umbrella header does not include the categories on UIImageView so the whole directory is declared as umbrella directory, while the iCarousel.h has just on header anyway so it is declared as umbrella header.

> **NOTE:** Once again, we would recommend you simply target iOS8+ and `use_frameworks!`.

#### Using the AFNetworking and iCarousel
We will implement the carousel app in a few lines of JavaScript:

![iCarousel](CocoaPods-iCarousel.png)

Use the following `app/main-page.xml`:
```XML
<Page xmlns="http://www.nativescript.org/tns.xsd" loaded="pageLoaded">
  <GridLayout rows="auto, auto, auto, *">
    <Label row="0" text="Tap the button" cssClass="title"/>
    <Button row="1" text="TAP" tap="{{ tapAction }}" />
    <Label row="2" text="{{ message }}" cssClass="message" textWrap="true"/>
    <Placeholder row="3" creatingView="createCarouselView" />
  </GridLayout>
</Page>
```

Along with the `app/main-page.js`:
``` JavaScript
var vmModule = require("./main-view-model");
function pageLoaded(args) {
    var page = args.object;
    page.bindingContext = vmModule.mainViewModel;
}
exports.pageLoaded = pageLoaded;

var CarouselData = NSObject.extend({
    init: function() {
        NSObject.prototype.init.apply(this, arguments);
        this._items = ['4zSb0qS', 'jCUvdej', 'bq7JddZ', 'K815GK5', 'GTeMJud', 'SEUNWpX'];
        return this;
    },

    numberOfItemsInCarousel: function(carousel) {
        console.log("Numbers of items: " + this._items.length);
        return this._items.length;
    },

    carouselViewForItemAtIndexReusingView: function(carousel, index, view) {
        console.log("Item at index: " + index);
        if (!view) {
            view = new UIImageView(CGRectMake(0, 0, 280, 175));
            var url = NSURL.URLWithString('http://i.imgur.com/' + this._items[index] + '.jpg');
            view.setImageWithURL(url);
            view.contentMode = UIViewContentMode.UIViewContentModeScaleAspectFit;
        }
        return view;
    }
}, {
    protocols: [iCarouselDataSource, iCarouselDelegate]
});

var carousel;
var carouselData;

function createCarouselView(args) {
    carousel = new iCarousel();
    carousel.type = iCarouselType.iCarouselTypeCoverFlow2;

    carouselData = CarouselData.alloc().init();

    carousel.delegate = carouselData;
    carousel.dataSource = carouselData;

    args.view = carousel;
    console.log("Created carousel: " + carousel);
}
exports.createCarouselView = createCarouselView;
```

#### What is Next
We are looking into integrating pods with our plug-in mechanism. In the long term, we should be able to provide a cross platform abstraction on frameworks, such as the GoogleMaps SDK, so you can simply set them up in XML, and use them across all supported mobile platforms.