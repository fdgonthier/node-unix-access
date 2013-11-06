# unix-access

`Node.js` module wrapping `access()` UNIX C function, which is currently missing in both V8 and Node's fs API. Both `sync` and `async` implementations are available.

`OS X` and `Linux` platforms supported.

# Introduction

Ever needed to find out whether your app has specific permissions to a path? Now you can check a path for `read`, `write` and `execute/search` permissions on files and directories.

For more information about UNIX `access()` function, run `man access` in your OS.

# Usage

Just issue `require('unix-access')` and you can run two functions available: `sync` and `async`.

### Examples

```js

    var access = require('unix-access');    
    var result;

    // synchronous checks
    result = access.sync('/var/log/your_app.log', '');     // existence of the file
    result = access.sync('/var/log/your_app.log', 'rw');   // read and write permissions
    result = access.sync('/var/log/your_app_dir/', 'rwx'); // full control

    // asynchronous check for read and executable permissions
    access.async('/bin/tar', 'rx', function(err, result) {
        console.log(result);
    });

```

### sync(path, permissions)

Both arguments are of `String` type. Return value is of `Boolean` type.

Permissions may contain case-insensitive combination of `r`, `w`, and `x` characters. Any other characters will be stripped out.

`True` return value indicates you do have the permissions you were testing the path for, `False` means you do not.

### async(path, permissions, callback)

Works the same way as `sync` version but asychronously.

Two arguments will be passed over the `callback(err, result)` function. Err is of `String` type, result of `Boolean` type.

# Installation

`npm install unix-access`

# Supported platforms and requirements

### OS X

Tested on OS X Mavericks 10.9.

Requires:

* Command Line Tools installed either separately or via Xcode
* Python 2.7 (present in the OS by default)

### Linux

Tested on Debian Wheezy 7.0.

Requires:

* make
* Python 2.7

# Dependencies

The module does not depend on any other code. For developers of this tool, `mocha` and `should` are the only dependencies for running unit tests.

# Technical details

Once issuing `npm install unix-access`, npm copies the module into node_modules directory and then executes `node-gyp rebuild` command to compile C++ sources. `node-gyp` is bundled within npm itself so you don't need to install it separately.

The C++ code being built by `node-gyp` is a gue between Google's V8 engine and native OS access() call defined in <unistd.h>.

Async function is really asynchronous (NOT just issuing callback in `process.nextTick()`). `uv_queue_work()` call from `libuv` manages the asynchronous execution in a separate thread pool.

# Development

In order to develop this module, these steps are required:

* Clone the git repo by executing `git clone URL`
* Go to the newly created directory in Terminal
* Run `npm install` to download dependencies
* Run unit tests by `npm test`
