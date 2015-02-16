# Modules

Folder structure:

```
.
├── app
│   ├── bootstrap.js
│   └── module
│       ├── index.js
│       └── module_utils.js
└── tns_modules
    └── camera
        └── index.js
```

## require

* `require('module');` - Resolved to `tns_modules/module`.
* `require('/private/var')` - Resolved as absolute path from the root of the device filesystem.
* `require('./module')` - Resolved to `module` relative to the file calling `require`.
* `require('../module')` - Resolved to `module` in the parent directory relative to the file calling `require`.

* If directory `module` exists:
  * If `module/package.json` exists and there is a `main: 'main-file.js'` file specified:
    * Opens `module/main-file.js`.
  * Else:
    * Opens `module/index.js`.
* Else if a file `module.js` exist:
  * Opens `module.js`.

Modules are cached so no cycles can happen.
