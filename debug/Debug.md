---
nav-title: "Debug on Device and Simulator"
title: "Debug on Device and Simulator"
description: "Debugging on USB Connected Device or Simulator"
position: 0
---

# Debugging
NativeScript for iOS provides support for JavaScript debugging using the [CLI](https://github.com/NativeScript/nativescript-cli) on Mac computer:
 - On iOS device (iPhone, iPad, iPod) connected over USB
 - On Simulator

The following CLI commands expect you have created an app and added the ios platform.

## Start App with Debugger
To build, deploy, run the app, and simultaneously start App Inspector, on device:
```bash
tns debug ios
```
On simulator:
```bash
tns debug ios --emulator
```

## Attach to an Already Running App
Should your app be already running on a device that is connected through the USB, you can attach a debugger:
```bash
tns debug ios --start
```
Or if your app is already running in an emulator:
```bash
tns debug ios --start --emulator
```
The command will start App Inspector and connect to the running app.

## Debugging Traffic
### Connecting to Device
The debugging traffic will be networked through localhost:8080 to the device port 8080.

### Connecting to Emulator
App Inspector will connect to the emulator on localhost:8080.

> **NOTE:** Currently we do not support changing the port numbers so if these ports are reserved from other applications the Inspector may fail to connect.