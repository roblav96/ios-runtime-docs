---
nav-title: "AppInspector"
title: "AppInspector"
description: "Describing AppInspector and it's features"
position: 2
---

# AppInspector
AppInspector is a powerful tool that makes it easy to inspect every resource of your application. With AppInspector, you see Safari's developer tools in a clean, unified interface, placing each of it's core functions in separate tabs. For more information on how to build, deploy, run the app, and simultaneously start App Inspector, click [here](https://github.com/NativeScript/nativescript-cli/blob/master/docs/man_pages/project/testing/debug.md).

AppInspector for iOS provides support for the following core functionalities

 - Resources
     Resources Tab lets you find every resources of your application folder, including documents, images, scripts, stylesheets and other resources that make up your application, in a list organized by folder structure. The Resources sidebar shows information about the selected resource. You can view the URL of the resource, as well as it's MIME type. The Resource Tab has [two great tools](https://webkit.org/blog/3846/type-profiling-and-code-coverage-profiling-for-javascript/) designed to make debugging JavaScript programs easier
     - The Code Coverage Profiler - Shows you exactly which parts of your program have run, and which haven't
     - The Type Profiler - Shows you type annotations next to important variables and function return types
     ![Resources Tab](resources_tab.png)
 - Timelines
     Timelines are a graphical representation of all JavaScript activities that occur during the lifetime of your application. You can start recording timelines in two ways 
     - Clicking the Start Recording button. This shows all activity that happens since the record button is clicked. To stop the profile, click the record button again ![Start recording](start_recording.png)
     - By including a ```console.profile()``` in your script. To stop profiling include ```console.profileEnd()``` in your script or evaluate it in the Console

    Once you have captured one or more profiles, they are listed on the left side of Web Inspector. Every profiler shows the time spend in each function execution grouped in three categories Self, Total and Average time. Where applicable, the source URL and line number of the function declaration is shown to the right of the function name. The source URL is a link. Clicking it opens the source in the content browser, scrolled to the line number where the function is declared.

 - Debugger
     If you are getting JavaScript errors on your webpage, you can use the Debugger navigation sidebar to assist you in finding the cause of the problem. By setting breakpoints throughout your code, you can inspect the values of your variables and observe the call stack during runtime.
Even if your JavaScript is minified, Web Inspector pretty-prints—or expands—all of your scripts, allowing you to set breakpoints on minified content.

 - Console
     The console offers a way to log diagnostic information to help debug your application. 
