# How to Rime with Squirrel

> Instructions to build Squirrel - the Rime frontend for macOS

## Manually build and install Squirrel

### Prerequisites

Install **Xcode 14.0** or above from App Store, to build Squirrel as a Universal
app.

Install **cmake**.

Download from https://cmake.org/download/

or install from [Homebrew](http://brew.sh/):

``` sh
brew install cmake
```

or install from [MacPorts](https://www.macports.org/):

``` sh
port install cmake
```

### Checkout the code

``` sh
git clone --recursive https://github.com/rime/squirrel.git

cd squirrel
```

Optionally, checkout Rime plugins (a list of GitHub repo slugs):

``` sh
bash librime/install-plugins.sh rime/librime-sample # ...
```

Popular plugins include [librime-lua](https://github.com/hchunhui/librime-lua), [librime-octagram](https://github.com/lotem/librime-octagram) and [librime-predict](https://github.com/rime/librime-predict)

### Shortcut: get the latest librime release

You have the option to skip the following two sections - building Boost and
librime, by downloading the latest librime binary from GitHub releases.

``` sh
bash ./action-install.sh
```

When this is done, you may move on to [Build Squirrel](#build-squirrel).

### Install Boost C++ libraries

Choose one of the following options.

**Option:** Download and install from source.

``` sh
export BUILD_UNIVERSAL=1

bash librime/install-boost.sh

export BOOST_ROOT="$(pwd)/librime/deps/boost-1.84.0"
```

Let's set `BUILD_UNIVERSAL` to tell `make` that we are building Boost as
universal macOS binaries. Skip this if building only for the native architecture.

After Boost source code is downloaded and a few compiled libraries are built,
be sure to set shell variable `BOOST_ROOT` to its top level directory as above.

You may also set `BOOST_ROOT` to an existing Boost source tree before this step.

**Option:** Install the current version form Homebrew:

``` sh
brew install boost
```

**Note:** with this option, the built Squirrel.app is not portable because it
links to locally installed libraries from Homebrew.

Learn more about the implications of this at
https://github.com/rime/librime/blob/master/README-mac.md#install-boost-c-libraries

**Option:** Install from [MacPorts](https://www.macports.org/):

``` sh
port install boost -no_static
```

### Build Squirrel

* Make sure you have updated all the dependencies. If you cloned squirrel with the command in this guide, you've already done it. But if not, this command will update submodules.

```
git submodule update --init --recursive
```

* There are a few environmental variables that you can define. Here's a list and possible values they may take:

``` sh
export BOOST_ROOT="path_to_boost" # required
export DEV_ID="Your Apple ID name" # include this to codesign, optional
export BUILD_UNIVERSAL=1 # set to build universal binary
export PLUM_TAG=":preset” # or ":extra", optional, build with a set of plum formulae
export ARCHS='arm64 x86_64' # optional, if not defined, only active arch is used
export MACOSX_DEPLOYMENT_TARGET='13.0' # optional, lower version than 13.0 is not tested and may not work properly
```

* With all dependencies ready, build `Squirrel.app`:

``` sh
make
```

* You can either define the environment variables in your shell/terminal, or append them as arguments to the make command. For example:

``` sh
# for Universal macOS App
make ARCHS='arm64 x86_64' BUILD_UNIVERSAL=1
```

## Install it on your Mac

### Make DMG (recommended)

Build a drag-and-drop DMG disk image — the standard macOS distribution format.
No PKG installer or postinstall scripts are required.

```
make dmg ARCHS='arm64 x86_64'
```

The resulting `package/Squirrel.dmg` contains `Squirrel.app` and a shortcut to
`/Library/Input Methods/`.  To install:

1. Open `Squirrel.dmg`.
2. Drag `Squirrel.app` onto the **Input Methods** shortcut.
3. Launch Squirrel from Launchpad or `/Library/Input Methods/`.  On the first
   launch the input source is registered and enabled automatically — no logout
   is needed.

Define `DEV_ID` to handle code signing and
[notarization](https://developer.apple.com/documentation/security/notarizing_macos_software_before_distribution)
automatically (Apple Developer ID required):

```
make dmg DEV_ID="Your Name" ARCHS='arm64 x86_64'
```

To make notarization work, save your credential under the same name first:

```
xcrun notarytool store-credentials 'Your Name'
```

You **don't** need `DEV_ID` if you are not distributing the build publicly.

### Make PKG (legacy)

The PKG-based installer is still available for workflows that require it:

```
make package ARCHS='arm64'
```

### Directly Install

Once built, you can install directly on your Mac for development:

``` sh
# Squirrel as a Universal app
make install
```

## Clean Up Artifacts

After installation or after a failed attempt, you may want to start over. Before you do so, **make sure you have cleaned up artifacts from previous build.**

To clean **Squirrel** artifacts, without touching dependencies, run:

``` sh
make clean
```

To clean up **dependencies**, including librime, librime plugins, plum and sparkle, run:

``` sh
make clean-deps
```

To clean up **packages**, run:

``` sh
make clean-package
```

If you want to clean all above, do all.

That's it, a verbal journal. Thanks for riming with Squirrel.
