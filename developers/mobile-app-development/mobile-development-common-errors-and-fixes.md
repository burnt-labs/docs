# Mobile Development – Common Errors & Fixes

When developing mobile applications for Android or iOS, encountering build errors is a regular part of the process. These errors can stem from SDK misconfigurations, dependency issues, or quirks in cross-platform libraries like React Native. Below is a list of common issues developers face, along with tested solutions.



### 1. Broken or Missing Build Tools (Android)

**Error:**

```
Build-tool 35.0.0 is missing AAPT at /.../build-tools/35.0.0/aapt

FAILURE: Build failed with an exception.
> Installed Build Tools revision 35.0.0 is corrupted. Remove and install again using the SDK Manager.
```

**Symptoms:**

* Gradle build fails almost immediately.
* Compilation of Java sources (`:app:compileDebugJavaWithJavac`) cannot proceed.
* Error points to a corrupted or incomplete installation of Build Tools.

**Solution:**

1. Open **Android Studio**.
2. Go to **SDK Manager → SDK Tools**.
3. Uncheck and re-check **Android SDK Build-Tools 35.0.0**, then click **Apply** to reinstall.
4.  Alternatively, run:

    ```bash
    sdkmanager --uninstall "build-tools;35.0.0"
    sdkmanager "build-tools;35.0.0"
    ```



### 2. `expo run:android` Fails – Emulator Not Detected

**Error:**

```
adb.exe: device 'emulator-5554' not found
CommandError: Failed to get properties for device (emulator-5554)
```

**Likely Cause:**

* The Android Debug Bridge (ADB) cannot find a running emulator.
* Emulator may not be initialized or started properly.

**Solution:**

1. Start Android Studio.
2. Go to **Tools → Device Manager**, then **launch a virtual device**.
3.  Verify the emulator is listed using:

    ```bash
    adb devices
    ```

    You should see a list like:

    ```
    List of devices attached
    emulator-5554   device
    ```

**Note:** If no devices are listed, try restarting ADB:

```bash
adb kill-server
adb start-server
```



### 3. Path Handling Errors in `react-native-libsodium` (Windows)

**Error Symptoms:**

* Build fails due to invalid escape sequences (`\A`, `\b`, etc.).
* Common when project paths include backslashes (e.g., `D:\Application\...`).

**Root Cause:**\
CMake interprets `\\` in Windows-style paths as escape sequences. This causes malformed paths when building native dependencies.

**Solution:**\
Manually modify the `CMakeLists.txt` file in the `react-native-libsodium` dependency:

**File Path:**

```
node_modules/react-native-libsodium/android/CMakeLists.txt
```

**Fix:**\
Add the following lines to convert `NODE_MODULES_DIR` into a valid CMake path:

```cmake
file(TO_CMAKE_PATH "${NODE_MODULES_DIR}" SAFE_NODE_MODULES_DIR)
set(JSI_CPP_PATH "${SAFE_NODE_MODULES_DIR}/react-native/ReactCommon/jsi/jsi/jsi.cpp")
```

Then replace references to `NODE_MODULES_DIR` with `SAFE_NODE_MODULES_DIR`. For example:

```javascript
# set the base libsodium build directory
set(LIBSODIUM_BUILD_DIR ${CMAKE_CURRENT_LIST_DIR}/../libsodium/build)

# set the libsodium build directory depending on the target abi
if (${ANDROID_ABI} STREQUAL arm64-v8a)
  set(LIBSODIUM_BUILD_DIR ${LIBSODIUM_BUILD_DIR}/libsodium-android-armv8-a+crypto)
elseif(${ANDROID_ABI} STREQUAL armeabi-v7a)
  set(LIBSODIUM_BUILD_DIR ${LIBSODIUM_BUILD_DIR}/libsodium-android-armv7-a)
elseif(${ANDROID_ABI} STREQUAL x86)
  set(LIBSODIUM_BUILD_DIR ${LIBSODIUM_BUILD_DIR}/libsodium-android-i686)
else()
  set(LIBSODIUM_BUILD_DIR ${LIBSODIUM_BUILD_DIR}/libsodium-android-westmere)
endif()

add_library(sodium SHARED IMPORTED)
include_directories( ${LIBSODIUM_BUILD_DIR}/include/ )
set_target_properties( sodium PROPERTIES IMPORTED_LOCATION ${LIBSODIUM_BUILD_DIR}/lib/libsodium.so )

# --- BEGIN MODIFICATION ---
# Convert NODE_MODULES_DIR to a CMake-friendly path
file(TO_CMAKE_PATH "${NODE_MODULES_DIR}" SAFE_NODE_MODULES_DIR)

# Construct the JSI CPP path using the safe path and forward slashes
set(JSI_CPP_PATH "${SAFE_NODE_MODULES_DIR}/react-native/ReactCommon/jsi/jsi/jsi.cpp")

# here we define a library target called "libsodium"
# which will be built from the listed source files
add_library(libsodium
  SHARED
  "${JSI_CPP_PATH}" # Use the corrected path variable
  ../cpp/react-native-libsodium.cpp
  ../cpp/react-native-libsodium.h
  cpp-adapter.cpp
)
# --- END MODIFICATION ---

# link the sodium lib with our "libsodium" library target defined earlier
target_link_libraries(libsodium sodium)
```

**After saving the file:**\
Clean and rebuild your Android project:

```bash
cd android
./gradlew clean
cd ..
npx expo run:android
```

**Recommended Tip:**\
Avoid placing your project in folders with capital letters or special characters (e.g., `D:\Application\Xion`) to reduce CMake path issues.



### 4. ADB Shows Duplicate or Ghost Devices

**Symptoms:**

* Android Studio’s AVD only lists one emulator, but `adb devices` shows multiple.
* Build errors or confusion about which emulator is active.

**Cause:**

* Stale ADB device entries from previously deleted or corrupted emulator instances.

**Fix:**\
Restart the ADB server:

```bash
adb kill-server
adb start-server
adb devices
```

If this doesn't resolve it:

* Delete all unused emulators from **Android Studio → Device Manager**.
* Recreate new AVDs to avoid misconfigured devices.



### Extra Tips for Smooth Builds

* Always use **Node.js LTS versions** for React Native projects (avoid experimental versions like v23).
* Run `npx expo doctor` or `npx react-native doctor` regularly to diagnose config issues.
*   Keep your Expo CLI and dependencies up to date:

    ```bash
    npm install -g expo-cli
    npm outdated
    ```



### Still Stuck?

If you continue to encounter issues:

* Run the command with `--verbose` or `--stacktrace` to get detailed logs.
* Refer to: [https://docs.gradle.org](https://docs.gradle.org)
* Or open an issue on the relevant GitHub repo, such as:
  * [react-native-libsodium](https://github.com/serenity-kit/react-native-libsodium/issues)

