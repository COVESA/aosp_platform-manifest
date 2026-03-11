# COVESA AOSP repo manifests

This repository contains repo manifests to build COVESA AOSP distributions.

For instructions on how to download emulator binaries please refer to https://github.com/COVESA/aosp_device_covesa_emulator

## How to get source code

Initialize repo with our manifest:

```bash
$ mkdir -p ~/aosp/covesa-aosp && cd "$_"
$ repo init -u https://github.com/COVESA/aosp_platform-manifest -b android-15 --depth=1
```

`--depth=1` saves space and time. It creates a shallow clone, but you can still “unshallow” any repository on demand.

Fetch the sources:

```bash
$ repo sync -c -j10
```

`-c` only syncs current manifest branch, which can also save space and time.
`-j{n}` number of jobs to run in parallel (default: 0; based on number of CPU cores)

On checkout error try the following in alternating sequence till everything is checked out:

```bash
$ repo sync -j1 --fail-fast
$ repo sync -l
```

To unshallow only COVESA's additions only:

```bash
$ sed -nE 's/^\s*<project[^>]*path="([^"]+)".*/\1/p' .repo/manifests/covesa-additions.xml \
  | xargs -I{} git --git-dir="{}/.git" --work-tree="{}" fetch --unshallow
```

## How to build

### COVESA Emulator

> ⚠️ **TODO**
> This is not available on `android-15` branch yet.

```bash
$ source build/envsetup.sh
$ lunch covesa_emu_arm64_car-userdebug
$ m emu_img_zip
```
This creates following artifact: `out/target/product/emulator_arm64/sdk-repo-linux-system-images-eng.${USER}.zip`

### COVESA OEM VHAL

#### Build arm64 emulator:
```bash
$ source build/envsetup.sh
$ lunch sdk_car_arm64-trunk_staging-userdebug
$ m -j10 emu_img_zip
```

This creates following artifact: `out/target/product/emulator_car64_arm64/sdk-repo-linux-system-images.zip`


#### Build x86_64 emulator:

```bash
$ source build/envsetup.sh
$ lunch sdk_car_x86_64-trunk_staging-userdebug
$ m -j10 emu_img_zip
```

This creates following artifact: `out/target/product/emulator_car64_x86_64/sdk-repo-linux-system-images.zip`


`-j{n}` number of jobs to run in parallel (default: 0; based on number of CPU cores)

## How to distribute

### COVESA Emulator

Deployment details are part of [`device/covesa/emulator`](https://github.com/COVESA/aosp_device_covesa_emulator).

### COVESA OEM VHAL

Following environment variables are considered in the below example:

- `ANDROID_SDK_ROOT`: Path to Android's SDK root, e.g.:
  - Windows: `C:\Users\<YourUser>\AppData\Local\Android\Sdk`
  - Mac: `~/Library/Android/sdk`
  - Linux: `~/Android/Sdk`
- `ARCH`: Emulator's architecture, e.g. `arm64-v8a` or `x86_64` depending on which ZIP file you have
  unzipped (see above)

Unzip the above ZIP file(s) into `${ANDROID_SDK_ROOT}/system-images-android-35/android-automotive/`:
```
$ unzip sdk-repo-linux-system-images.zip -d ${ANDROID_SDK_ROOT}/system-images/android-35/android-automotive/
```
This will create the `${ARCH}` (`arm64-v8a` or `x86_64`) directory.

Add a `${ANDROID_SDK_ROOT}/system-images-android-35/android-automotive/${ARCH}/package.xml`. 
