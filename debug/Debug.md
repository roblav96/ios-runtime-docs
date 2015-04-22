---
nav-title: "Debug on Device and Emulator"
title: "Debug on Device and Emulator"
description: "Debugging on USB Connected Device or Emulator"
position: 0
---

# Debugging
NativeScript for iOS provides support for JavaScript debugging using the [CLI](https://github.com/NativeScript/nativescript-cli) on Mac computer:
 - On iOS device (iPhone, iPad, iPod) connected over USB
 - On Emulator

The following CLI commands expect you have created an app and added the ios platform. The inspector will open as a web page in Safari.

> **NOTE:** The minimum supported Safari version is 7.1.2.

## Start App with Debugger
To build, deploy, run the app, and simultaneously start a Safari with the inspector tools, on device:
```bash
tns debug ios --debug-brk
```
On emulator:
```bash
tns debug ios --debug-brk --emulator
```

## Attach to an Already Running App
Should your app be already running on a device that is connected through the USB, you can attach a debugger:
```bash
tns debug ios --start
```
Or if your app is allready running in an emulator:
```bash
tns debug ios --start --emulator
```
The command will start a Safari with the inspector tool and connect to the running app.

## Debugging Traffic
#### Connecting to Device
The debugging traffic will be networked from the Safari through localhost:8080 to the device port 8080.

#### Connecting to Emulator
The Safari will connect to the emulator on localhost:8080.

> **NOTE:** Currently we do not support changing the port numbers so if these ports are reserved from other applications the Inspector may fail to connect.