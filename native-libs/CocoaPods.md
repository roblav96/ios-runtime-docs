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

