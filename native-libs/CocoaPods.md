# Using CocoaPods

When you develop for iOS, you can quickly add third party libraries to your NativeScript projects via [CocoaPods](https://cocoapods.org/), a dependency manager for Swift and Objective-C Cocoa projects.

## Table of Contents
 * [Create CLI Project](#create-cli-project)
 * [Install CocoaPods](#install-cocoapods)
 * [Example: Using Google Maps SDK](#example-using-google-maps-sdk)
    * [Add Podfile with Dependencies](#add-podfile-with-dependencies)
	* [Google Maps Code](#google-maps-code)
 * [Example: Using the AFNetworking and iCarousel](#example-using-the-afnetworking-and-icarousel)
    * [CocoaPods use_frameworks or Module Maps](#cocoapods-use_frameworks-or-module-maps)
       * [Use Frameworks](#use-frameworks)
	   * [Module Maps](#module-maps)
	* [Carousel Code](#carousel-code)
 * [What is Next](#what-is-next)

## Create CLI Project
To start, create a project, add the iOS platform and prepare the iOS platform.
```bash
$ tns create MYCocoaPods
$ cd MYCocoaPods
$ tns platform add ios
$ tns prepare ios
```
The CLI generates an Xcode project for you in `platforms/ios/MYCocoaPods.xcodeproj`.

## Install CocoaPods
You need to install CocoaPods. If you haven't yet, you can do so by:
```
$ sudo gem install cocoapods
```
> **NOTE:** All operations and code in this article are verified against CocoaPods 0.37.1.

You can check your current version using:
```
$ pod --version
```

To update CocoaPods just install the gem again with:
```
sudo gem install cocoapods
```

## Example: Using Google Maps SDK
The following example shows how to add the [Google Maps SDK for iOS](https://developers.google.com/maps/documentation/ios/) using CocoaPods in your project and display a simple map.

![CocoaPods-GoogleMaps.png](CocoaPods-GoogleMaps.png)

### Add Podfile with Dependencies
Create a [Podfile](https://guides.cocoapods.org/syntax/podfile.html) as sibling to the `platforms/ios/MYCocoaPods.xcodeproj`, with the following content:
``` Ruby
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, '8.1'
pod 'GoogleMaps'
```

Next install the pod dependencies:
```
$ cd platform/ios
$ pod install
```
This modifies the `MYCocoaPods.xcodeproj` and creates a workspace with the same name.

> **IMPORTANT:** You will no longer be able to run the `xcodeproj` alone or from the CLI. You need to run your project from the newly created workspace. Make sure to open the `MYCocoaPods.xcworkspace` in Xcode first. Next, in the terminal run `$ tns prepare ios` and then run the app from Xcode. We will provide support for workspaces in a future version of the CLI.

### Google Maps Code
To actually use the Google Maps you will have to provide an API key in `applicationDidFinishLaunchingWithOptions`. The cross-platform UI modules expose callback, that is invoked in our AppDelegate. There you can register an API key:
``` JavaScript
// app.js
var application = require("application");

application.onLaunch = function() {
    console.log('Providing Google Map API key...');
    // NOTE: Visit the Google documentation to learn how to obtain an API key: https://developers.google.com/maps/documentation/ios/start
    GMSServices.provideAPIKey("***************************************");
}

application.mainModule = "main-page";
application.cssFile = "./app.css";
application.start();
```

Next, place a map in the UI:
``` XML
<!-- main-page.xml -->
<Page xmlns="http://schemas.nativescript.org/tns.xsd" loaded="pageLoaded">
  <GridLayout rows="auto, auto, auto, *">
    <Label row="0" text="Tap the button" cssClass="title"/>
    <Button row="1" text="TAP" tap="{{ tapAction }}" />
    <Label row="2" text="{{ message }}" cssClass="message" textWrap="true"/>
    <Placeholder row="3" creatingView="createMapView" />
  </GridLayout>
</Page>
```

The following code initializes the map placeholder:
``` JavaScript
// main-page.js
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
    // NOTE: In-line functions such as CLLocationCoordinate2DMake are not exported.
    marker.position = { latitude: -33.86, longitude: 151.20 }
    marker.title = "Sydney";
    marker.snippet = "Australia";
    marker.map = mapView;

    console.log("Displaying map...");
    args.view = mapView;
}
exports.createMapView = createMapView;
```

## Example: Using the AFNetworking and iCarousel
We will implement a simple carousel app in a few lines of JavaScript using the [iCarousel](https://github.com/nicklockwood/iCarousel) pod.

![iCarousel](CocoaPods-iCarousel.png)

### CocoaPods use_frameworks or Module Maps
The NativeScript framework for iOS generates metadata from Objective-C headers. The APIs are exposed in JavaScript using an algorithm similar to how Swift exposes Objective-C. In the process, we need the relatively new clang modules declarations for Objective-C. They are declared using module map files.

You can find more information about module maps [here](http://clang.llvm.org/docs/Modules.html#umbrella-directory-declaration).

#### Use Frameworks
[CocoaPods now support Swift and dynamic frameworks.](http://blog.cocoapods.org/CocoaPods-0.36/) You can use the following CocoaPod [Podfile](https://guides.cocoapods.org/syntax/podfile.html) in `platforms\ios\Podfile`:
``` Ruby
platform :ios, '8.0'
use_frameworks!

pod 'iCarousel'
pod 'AFNetworking'
```

After you install the pods with the `use_frameworks!` enabled, the pods will be packed in dynamic frameworks. The upside is that each pod will have its `module.modulemap` declared so you would be good to go with no extra manual setup. The downside is these dynamic frameworks are supported in iOS 8+ and your app must target the iOS 8 SDK to be able to use them.

#### Module Maps
The Google Maps SDK had a nicely done module map placed in `Pods/GoogleMaps/Frameworks/GoogleMaps.framework/Modules/module.modulemap`. The module map is available in this location even if you have not set `use_frameworks!` in the Podfile. However, for some CocoaPods, you need to provide module maps by hand.

The following CocoaPods [Podfile](https://guides.cocoapods.org/syntax/podfile.html) does not set `use_frameworks!`:
```
platform :ios, '8.0'

pod 'iCarousel'
pod 'AFNetworking'
```

Running `$ pod install` pulls the two pods but does not generate module maps for them. If you want to use these libraries, you need to add the following module maps in the file `platforms/ios/module.modulemap`:
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

This should be enough to enable the two pods in your project. Note that the `AFNetworking.h` umbrella header does not include the categories on UIImageView. That's why the whole directory is declared as umbrella directory. The `iCarousel.h` has a single header, so it is declared as an umbrella header.

> **NOTE:** We would recommend you simply target iOS8+ and `use_frameworks!`.

### Carousel Code
To implement the carousel add the following UI declaration:
```XML
<!-- main-page.xml -->
<Page xmlns="http://schemas.nativescript.org/tns.xsd" loaded="pageLoaded">
  <GridLayout rows="auto, auto, auto, *">
    <Label row="0" text="Tap the button" cssClass="title"/>
    <Button row="1" text="TAP" tap="{{ tapAction }}" />
    <Label row="2" text="{{ message }}" cssClass="message" textWrap="true"/>
    <Placeholder row="3" creatingView="createCarouselView" />
  </GridLayout>
</Page>
```

Next, handle the carousel initialization:
``` JavaScript
// main-page.js
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
        console.log("Number of items: " + this._items.length);
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

## What is Next
We are looking into integrating pods with our [plug-in](https://github.com/NativeScript/NativeScript/issues/25) mechanism. In the long term, we should be able to provide a cross-platform abstraction on frameworks, such as the Google Maps SDK, so you can simply set them up in XML, and use them across all supported mobile platforms.
