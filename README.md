# i-Cook'in: restoring recipes and images

Fix for the Guy Demarle i-Cook'in (VR400) when recipes, favorites or pictures fail to load.

## Cause

The control panel is an Android 4.4 tablet with a CA store from 2014. The backend
`api.guydemarle.com` uses a ZeroSSL certificate chaining to USERTrust RSA, a root missing from
the tablet's store, so TLS validation fails. Recipe images, loaded through OkHttp, fail every
time.

## Fix

Add `cacerts/35105088.0` (USERTrust RSA Certification Authority) to the tablet's system CA store.
The file name follows the Android convention (`openssl x509 -subject_hash_old`).

## Prerequisites

- A PC with `adb`, from the
  [Android SDK Platform-Tools](https://developer.android.com/tools/releases/platform-tools)
  or a Linux distribution package (`android-tools-adb`, `android-tools`).
- A micro-USB cable that carries data. Charge-only cables do not show the device.

The tablet firmware is an `eng` build: `adb` is enabled, runs as root and `/system` is
writable. No developer options or unlocking are needed.

## Procedure

1. Unplug the robot from mains. Open the control panel and disconnect the tablet's micro-USB
   cable from the base.
2. Connect the tablet to the PC. `adb devices` lists it.
3. Push the certificate:
   ```sh
   adb push cacerts/35105088.0 /system/etc/security/cacerts/35105088.0
   adb shell chmod 644 /system/etc/security/cacerts/35105088.0
   adb shell am force-stop usb.terminal
   ```
4. Reconnect the tablet to the base and close the panel.

The change persists across reboots. To revert, delete
`/system/etc/security/cacerts/35105088.0`.

## Disclaimer

Done on your own device, at your own risk. Opening the robot may void the warranty.
