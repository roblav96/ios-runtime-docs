---
nav-title: "Use native libraries in iOS"
title: "How to use native libraries in iOS"
description: "Describes all the possible approaches for using native libraries in your NativeScript app."
position: 1
---

NativeScript for iOS supports including native libraries and consuming their APIs from JavaScript.

Basically there are tree types of "packages" in the iOS world:
 1. Static library (`libMyLib.a`) which comes with a headers folder (usually called `include`) containing `.h` files.
 2. Static framework (`MyFramework.framework`) which is an ordinary static library wrapped in a framework (most of them come without the needed `module.modulemap` file in it)
 3. Shared framework (`MyFramework.framework`) which is an ordinary shared library wrapped in a framework (with `module.modulemap` file incldued)

Theoretically speaking, NativeScript for iOS supports all of these (without even using plugins), but all of them require a manually editing the Xcode project file in `{your-app}/platforms/ios`, which often is not trivial and additionally `{your-app}/platforms/` folder is auto-generated and this is why it should not be submitted in VCS, so we don't encourage editing its content. The question is how to use a library without editing `platforms/` folder. Here come [NativeScript plugins](./../../../plugins.md) to the rescue. The main problem plugins solve is to enable you to use libraries without making manual changes to the Xcode project in `platforms/ios` folder.

Generally, there are three ways to add a library in your NativeScript project:
 1. [Create plugin containing a CocoaPod `Podfile`](./../../../plugins.md). (best option)
 2. [Create plugin containing the already built binary and headers](./../../../plugins.md). (good option)
 3. Don't use plugin and manually change the Xcode project in `{your-app}/platforms/ios/` folder. (NOT RECOMMENDED)

The recommended approach is to use NativeScript plugins with shared frameworks coming from `CocoaPods` but this is not always possible. The goal of this article is to makes it clear which of these options to use and how to apply them.

Basically, to consume a native library the iOS Runtime has to know about:
 1. Binary file (e.g `libMyLib.a`, `MyLib`).
 2. Header files and `module.modulemap` file  describing a clang module and specifing which headers are part of the module.

The only reason the runtime (in particular the metadata generator) needs header files is to generate metadata. The metadata generator knows which headers have to be parsed because of the supplied `module.modulemap` file. Both the headers and `module.modulemap` file must reside in a folder which is part of the header search paths of app's Xcode project (`{your-app}/platforms/ios/{your-app}.xcodeproj`). Example of `module.modulemap` file you can find [here](https://github.com/NativeScript/ios-runtime/blob/master/tests/TestFixtures/module.modulemap).

# Shared Framework

This is the best option because shared frameworks are supported by [NativeScript plugins](./../../../plugins.md) and can be added with `CocoaPods`. Using `CocoaPods` means you can remove the framework (with all the binary and header files in it) from your plugin's repository and keep only single `Podfile`. You also get all the benefits of using a package manager. In the context of NativeScript plugin, CocoaPods can only be used with shared frameworks because only they have a well knows structure and `module.modulemap` file included which eliminates the need of manual work.
If there is no `pod` for the current library you can still use a plugin, but the framework must be dropped in the plugin folder (`{your-plugin}/platforms/ios/{MyFramework}.framework`) and you lose all the benefits of using package manager.

Pros:
 1. Can be included by NativeScript plugin.
 2. Can be included in the plugin by a `Podfile` (if a `pod` for the library exists).
 3. There is no need to manually edit the library before adding it.
 4. There is no need to manually edit the app after adding the library.

Cons:
 1. Shared frameworks can be used only in iOS 8 and above. Bare in mind that this limitation is valid for pure native applications, too. If you are targeting iOS version lower than 8.0 you must use static framework.

# Static Framework

Most of the static frameworks don't contain `module.modulemap` file, so you have to add the file manually. This is why static frameworks can't be included by a `Podfile`. In fact, NativeScript CLI enforces all pods to be built as shared frameworks (by adding `use_frameworks!` to the `Podfile`). To include a static framework in a plugin grab a prebuilt version of the framework, add a `module.modulemap` file in it and drop it in your `{plugin-path}/platforms/ios/` folder.

Pros:
 1. Can be included by NativeScript plugin.
 2. There is no need to manually edit the app after adding the library (but you have to manually edit the framework in order to add `module.modulemap` file).

Cons:
 1. Manual changes of the framework are required (add `module.modulemap` file).
 2. Only Objective-C APIs are exposed (no C functions and C constant) from static frameworks. This limitation has a solution which includes manually editing the Xcode project file, so it should be used only as a last resort.

# Static Library
NativeScript CLI supports static libraries comming from plugins but the binary and headers must be ordered in a specific folder structure described in details [here](./../../../plugins.md). This is required because NativeScript CLI generates `module.modulemap` file for the library which works most of the time. In the other cases the library must be wrapped in a static framework with manually included `module.modulemap` file.

Pros:
 1. Can be included by NativeScript plugin.
 2. It works without manual changes but not in all cases.
 3. It is trivial to wrap a static library in a static framework. Just put all the headers and binary files in the proper folder structure, add `module.modulemap` file and here you go - you have a static framework, which works in all cases.

Cons:
 1. Can't be included by a `Podfile`.
 2. In some cases must add `module.modulemap` file manually.
 3. Must wrap the library in a static framework if the automatic `module.modulemap` file generation does not succeed.
 4. Only Objective-C APIs are exposed (no C functions and C constant) from static libraries. This limitation has a solution which includes manually editing the Xcode project file, so it should be used only as a last resort.

NativeScript plugins also support merging of `.plist` files, so if a library requires changes in `Info.plist` this can be done from a plugin without touching the `/platforms/ios/` folder. However, there are libraries which require more complex manipulations of the Xcode project file, which can't be achieved with plugins. In these cases the only solution is to do it manually. Bare in mind that after updating the iOS platform your manual changes may be gone.

# Conclusion

The rule of thumb approach is to avoid manual changes to Xcode project file in `/platforms/ios` folder. Try always to use CocoaPods with NativeScript plugins and shared frameworks. The second valid option is a prebuilt static framework with manually added `module.modulemap` file, wrapped in NativeScript plugin. Use the other options only as last resort after making sure there is no better solution.
