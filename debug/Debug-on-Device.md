---
nav-title: "Debug on Device"
title: "Debug on Device"
description: "Debugging on USB Connected Device"
position: 0
---

# Debugging on USB Connected Device
NativeScript for iOS provides support for JavaScript debugging. The current implementation supports debugging on device connected over USB using the [CLI](https://github.com/NativeScript/nativescript-cli) on Mac computer.

The following command:
```
tns debug ios --debug-brk
```
Will build, deploy, run the app on device, and simultaneously start a Safari with the inspector tools.

The debugging trafic will be networked from the Safari through localhost:8080 to the device's port 8080. Currently we do not support changing the port numbers so if these ports are reserved from other applications the Inspector may fail to connect.

