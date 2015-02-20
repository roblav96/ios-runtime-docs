# Remote Debugging for iOS

## Debugging on simulator

1. Add `TNSDebugging`(`/Source/TNSDebugging/TNSDebugging/TNSDebugging.xcodeproj`) as subproject to your app project in XCode.

2. Add `TNSDebugging` target as target dependency in your app project in XCode.

3. Add `libTNSDebugging.a` to "Link Binary With Libraries" list of your app project in XCode.

4. Copy these two lines

  	```
 	id server = [runtime startWebSocketServerOnPort:8080];
    CFRunLoopRun();
 	```
	in `main.m` after initializing the `TNSRuntime`. You also need to include `<TNSDebugging/TNSDebugging.h>` header file.

	```
	#include <TNSDebugging/TNSDebugging.h>

	...

	TNSRuntime *runtime = [[TNSRuntime alloc] initWithApplicationPath:[[NSBundle mainBundle] bundlePath]];
	[TNSRuntimeInspector setLogsToSystemConsole:YES];

	// Initialize the web socket server for remote debugging
	id server = [runtime startWebSocketServerOnPort:8080];
	CFRunLoopRun();

	JSValueRef error = NULL;
	[runtime executeModule:@"./bootstrap" error:&error];

	...

 	```

5. Run the app on simulator. The app should print in the XCode console `Waiting for debugger...` and the screen should be black. The app is listening on localhost:8080, waiting for WebInspector to connect to it.

6. Run the WebInspector by executing `/Source/TNSDebugging/scripts/WIRunForSafari.sh` bash script. If you run it for first time you must first build it by executing `/Source/TNSDebugging/scripts/WIBuildForSafari.sh` bash script. The built product can be found in `/Source/TNSDebugging/TNSDebugging/WebInspectorUI-600.1.4/build/Release`.

	```
	$ ./WIBuildForSafari.sh
	$ ./WIRunForSafari.sh
	```
	The WebInspector should be opened in Safari.

7. The black screen should be replaced by the app main screen and now you are able to debug the JS code from the WebInspector.

## Debugging on device

Debugging on device is the same as debugging on simulator with the only difference that `WIRunForSafari.sh` script should be run with `-d` flag.

	./WIRunForSafari.sh -d

The script will run `iProxy` tool to proxy port 8080 on the Mac to port 8080 on the device. The WebInspector is always connecting to localhost:8080. After finish the debug session don't forget to close the process (by Ctrl + C), otherwise port 8080 will not be freed and you will be unable to debug on simulator.

## Dependencies

- Safari 7.1 or greater

- To debug from the device you must install `usbmuxd` tool. It can be installed with `Homebrew Package Manager` by running the command:

	```
	$ brew install usbmuxd
	```

## Possible issues


- If after running the WebInspector the Web Development Tools are not rendered in Safari, check your version of Safari. It must be 7.1 or greater.

- If after starting the app the message `Waiting for debugger...` is never logged in the XCode console, maybe the port 8080 is used by other process. Run `/Source/TNSDebugging/scripts/WIProcessesOnPort.sh 8080` script to view all processes which are using the port and `/Source/TNSDebugging/scripts/WIFreePort.sh 8080` to stop them. After stopping all processes which are using port 8080, run the app again.

- If after starting the app the message `Waiting for debugger...` is not logged and the app crashes with "[TNSRuntime startWebSocketServerOnPort:]: unrecognized selector sent", add `-ObjC` to `Build Settings -> Other Linker Flags` in your app project.

## Notes

There is no need to do all the steps when you want to debug your app. You can create a new target in your app project and set all the settings in it. Now you can debug the app by starting the specified target and running the WebInspector.
