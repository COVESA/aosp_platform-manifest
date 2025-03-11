# COVESA AOSP repo manifests

This repository contains repo manifests to build COVESA AOSP distributions.

Binary distributions can be found here instead: TBD

## How to get source code

Initialize repo with our manifest:

```bash
mkdir -p ~/aosp/covesa-android-12.0.0 && cd "$_"
repo init -u https://github.com/COVESA/aosp_platform-manifest -b android-12 --depth=1
```

`--depth=1` saves space and time. It creates a shallow clone, but you can still “unshallow” any repository on demand.

Fetch the sources:

```
repo sync -c
```

`-c` only syncs current manifest branch, which can also save space and time.

## How to build

Prepare build:

```bash
source build/envsetup.sh
lunch
```

Select the target from `lunch` menu. For example, `covesa_emu_x86_64_car-userdebug`. Then start the build:

```
m
```