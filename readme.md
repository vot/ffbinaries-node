# ffbinaries downloader

[![NPM Version][npm-img]][npm-url]
[![NPM Downloads][npm-dl-img]][npm-url]

[npm-url]: https://npmjs.org/package/ffbinaries
[npm-img]: https://img.shields.io/npm/v/ffbinaries.svg
[npm-dl-img]: https://img.shields.io/npm/dm/ffbinaries.svg


Downloads precompiled **ffmpeg, ffprobe, ffplay and ffserver binaries** from http://ffbinaries.com.


This module is cross-platform and can be used programatically, i.e. as a build step or in a postinstall script.

<br />

**Important: Syntax change in 1.0.0. Please update.**

If you're still using an older version you can see the
[previous syntax documented here](https://github.com/vot/ffbinaries-node/blob/ccad244c9fb64e2d90a9c788bf3a726f9df15f10/readme.md).

Old syntax had platform as the main argument and components as flag/option.

Their order is now reversed, i.e. `ffbinaries ffmpeg ffplay -p win-64`.

This applies to both [command line interface](#cli)
and [programmatical usage](#programatically).

<br />

If you're experiencing issues please update to the newest version and run `ffbinaries clearcache`.


# New in version 1.0.0

* Updated syntax (see note above)
* Cleaned up code and implemented various small fixes (handling errors in callbacks, added ticker function)


# Platforms

The following platform codes are available:

## Windows
**windows-32** (aliases: win, windows, win-32), **windows-64** (alias: win-64)

## Linux
**linux-32** (alias: linux), **linux-64**, **linux-armhf** (alias: linux-arm), **linux-armel**

## OS X
**osx-64** (aliases: mac, osx, mac-64)

You can use aliases as your platform code argument in both CLI and programatically.

# Included components

|          | Mac | Linux | Windows |
|----------|-----|-------|---------|
| ffmpeg   | v   | v     | v       |
| ffprobe  | v   | v     | v       |
| ffserver | v   | v     |         |
| ffplay   | v   | v*    | v       |

(* Only linux-32 and linux-64 builds of ffplay are currently available)

# Usage

You can run it from your code or through CLI.

If `output` argument is specified the binary will be placed there.
In CLI it will default to current working directory.
Programatically the default is the also the working directory.

If `platform` argument is missing then the current platform will be autodected and binaries for it will be downloaded.

If `components` argument is missing then binaries of all available components will be downloaded (see [Included components](#included-components) section).


## CLI

When installed globally with `npm i ffbinaries -g` this module will register
itself on command line interface.

### Arguments

CLI uses the following syntax:

`ffbinaries [components] [--platform] [--output] [--quiet] [--version]`

Each flag can also be abbreviated in your scripts with `-p`, `-o`, `-q` and `-v` respectively.

### Examples

**Download all binaries for your platform**

`ffbinaries`


**Download all binaries for a specified platform**

`ffbinaries -p=mac`


**Download only ffplay for 64-bit Windows, quiet output**

`ffbinaries ffplay -p=win-64 --quiet`


**Download only ffmpeg and ffprobe, version 3.2 for 64-bit Linux, quiet output, save binaries in a specified directory**

`ffbinaries ffmpeg ffprobe -p=linux-64 -q -v=3.2 --output=/usr/local/bin`

**Additional commands**

There are also `ffbinaries help`, `ffbinaries versions` and `ffbinaries clearcache`.


## Programatically

### Methods

`ffbinaries` library exports the following methods:

* `downloadFiles(platform, opts, callback)` downloads and extracts the requested binaries.

   The `opts` parameter is an object that can contain these optional parameters:
      * `destination`: the path where the binaries will be downloaded to. If not provided it will default to `.`.
      * `components`: an array of string values that describes which [components](#included-components) to download. If not provided it will default to all components available for the platform.
      * `version`: version of ffmpeg to download
      * `quiet`: suppress verbose logs

* `getVersionData(version, callback)` fetches the full data set without downloading any binaries.

* `listVersions(callback)` returns the list of available versions from the API

* `listPlatforms()` returns the list of available platforms

* `detectPlatform()` returns the platform code of the machine as detected by the module.

* `resolvePlatform(input)` resolves input to a platform code (matches aliases).

* `clearCache()` purges local cache


### Examples

**Download all binaries for your platform**

```
var ffbinaries = require('ffbinaries');
var platform = ffbinaries.detectPlatform();

ffbinaries.downloadFiles(function () {
  console.log('Downloaded all binaries for ' + platform + '.');
});
```

**Download only ffplay for your platform**

```
var ffbinaries = require('ffbinaries');
var platform = ffbinaries.detectPlatform();

ffbinaries.downloadFiles('ffplay', function () {
  console.log('Downloaded ffplay binary for ' + platform + '.');
});
```

**Download only ffmpeg and ffprobe, version 3.2 for 64-bit Linux, quiet output, save binaries in a specified**

```
var ffbinaries = require('ffbinaries');
var dest = __dirname + '/binaries';

ffbinaries.downloadFiles(['ffmpeg', 'ffprobe'], {platform: 'linux-64', quiet: true, destination: dest}, function () {
  console.log('Downloaded ffplay and ffprobe binaries for linux-64 to ' + dest + '.');
});
```

## Data source

The API backend is located at http://ffbinaries.com and is powered by this app: https://github.com/vot/ffbinaries-api

The binaries are hosted on GitHub as releases of https://github.com/vot/ffbinaries-prebuilt repo.
