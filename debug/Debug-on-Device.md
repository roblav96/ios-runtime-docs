---
nav-title: "Debug on Device"
title: "Debug on Device"
description: "Debugging on USB Connected Device"
position: 0
---

# Debugging on USB Connected Device
NativeScript for iOS provides support for JavaScript debugging. The current implementation supports debugging on iOS device (iPhone, iPad, iPod) connected over USB using the [CLI](https://github.com/NativeScript/nativescript-cli) on Mac computer.

The following CLI commands expect you have created an app and added the ios platform. The inspector will open as a web page in Safari.

> **NOTE:** The minimum supported Safari version is 7.1.2.

## Start App with Debugger
The following command:
```bash
tns debug ios --debug-brk
```
Will build, deploy, run the app on device, and simultaneously start a Safari with the inspector tools.

## Attach to Already Running App
Should your app be already running on a device that is connected through the USB, you can attach a debugger using:
```bash
tns debug ios --start
```
The command will start a Safari with the inspector tool and connect to the running app.

## Debugging Traffic
The debugging traffic will be networked from the Safari through localhost:8080 to the device port 8080. Currently we do not support changing the port numbers so if these ports are reserved from other applications the Inspector may fail to connect.
