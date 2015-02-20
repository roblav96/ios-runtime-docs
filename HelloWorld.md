---
nav-title: "Hello World Application"
title: "Hello World Application"
description: "A simple hello-world application"
position: 0
---

In `bootstrap.js`:

```javascript
var RootViewController = UIViewController.extend({
    viewDidLoad: function() {
        UIViewController.prototype.viewDidLoad.apply(this, arguments);

        var label = new UILabel(CGRectMake(0, 0, 250, 60));
        label.text = "Hello, World!";

        label.center = this.view.center;
        label.textAlignment = UITextAlignment.UITextAlignmentCenter;

        this.view.addSubview(label);
    }
});

var AppDelegate = UIResponder.extend({
    applicationDidFinishLaunchingWithOptions: function(application, launchOptions) {
        this._window = new UIWindow(UIScreen.mainScreen().bounds);
        this._window.backgroundColor = UIColor.whiteColor();
        this._window.rootViewController = new RootViewController();
        this._window.makeKeyAndVisible();
        return true;
    }
}, {
    protocols: [ UIApplicationDelegate ]
});

UIApplicationMain(0, null, null, NSStringFromClass(AppDelegate.class()));
```
