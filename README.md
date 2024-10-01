# Capacitor LiDAR and ToF Plugin

A Capacitor plugin to access **LiDAR sensors on iOS devices** and **ToF sensors on Android devices** for 3D scanning, depth sensing, and related applications.

## Features
- **iOS (LiDAR)**: Access LiDAR sensors on supported iOS devices.
- **Android (ToF)**: Check for and use Time of Flight (ToF) sensors on Android devices.
- **Permission Handling**: Built-in handling for camera permissions on both platforms.
- **Real-time Depth Data**: Capture and use depth data from sensors.

## Requirements

- **Capacitor** version 3 or later
- iOS 13.0 or higher (with LiDAR support)
- Android 6.0 (API level 23) or higher (with ToF sensor support)

## Installation

Install the plugin using npm:

```bash
npm install @your-org/capacitor-lidar-tof
```

Then, sync the plugin with your Capacitor project:

```bash
npx cap sync
```

## iOS Setup

For iOS, ensure that your app requests camera access since the LiDAR sensor uses the camera.

1. **Add Camera Usage Description**

In `ios/App/App/Info.plist`, add the following entry to explain why your app needs camera access:

```xml
<key>NSCameraUsageDescription</key>
<string>This app requires camera access to use the LiDAR sensor for depth scanning.</string>
```

2. **Platform-Specific LiDAR Requirement**

Ensure that you are running this on a LiDAR-capable iPhone, such as iPhone 12 Pro or later.

## Android Setup

On Android, ToF sensors are part of the camera system, so you need to request camera permissions.

1. **Add Permissions to `AndroidManifest.xml`**

In `android/app/src/main/AndroidManifest.xml`, add the following permission:

```xml
<uses-permission android:name="android.permission.CAMERA" />
```

2. **Runtime Permission Handling**

The plugin automatically handles runtime permissions for Android 6.0 (API level 23) and higher.

## Usage

### 1. **Requesting Camera Permissions**

Both LiDAR and ToF require camera access. Here’s how you can request the necessary permissions in your app:

```typescript
import { Plugins } from '@capacitor/core';
const { MyLidarPlugin } = Plugins;

// Request camera permission (for both platforms)
async function requestCameraPermission() {
  try {
    const permissionResult = await MyLidarPlugin.requestCameraPermission();
    console.log(permissionResult.status);  // Camera access granted or already granted
  } catch (error) {
    console.error('Permission error:', error);
    alert('Camera access is required to use the sensor.');
  }
}

requestCameraPermission();
```

### 2. **Starting a LiDAR Session (iOS)**

For iOS devices that support LiDAR, you can start a LiDAR session like this:

```typescript
async function startLidarSession() {
  try {
    const result = await MyLidarPlugin.startLidarSession();
    console.log(result.status);  // LiDAR session started successfully
  } catch (error) {
    console.error('LiDAR session error:', error);
  }
}

startLidarSession();
```

### 3. **Getting LiDAR Depth Data (iOS)**

Once the LiDAR session is active, you can get the depth data:

```typescript
async function getLidarData() {
  try {
    const data = await MyLidarPlugin.getLidarData();
    console.log(`LiDAR depth data: Width - ${data.width}, Height - ${data.height}`);
  } catch (error) {
    console.error('Error retrieving LiDAR data:', error);
  }
}

getLidarData();
```

### 4. **Checking for ToF Sensor Support (Android)**

On Android, you can check whether the device supports a ToF sensor before starting a session:

```typescript
async function checkToFSupport() {
  try {
    const result = await MyLidarPlugin.checkToFSupport();
    if (result.tofSupported) {
      console.log('ToF sensor is supported');
      startToFSession();  // You can now start a ToF session
    } else {
      console.log('ToF sensor is not supported');
    }
  } catch (error) {
    console.error('Error checking ToF support:', error);
  }
}

checkToFSupport();
```

### 5. **Starting a ToF Session (Android)**

If the device supports a ToF sensor, start the session like this:

```typescript
async function startToFSession() {
  try {
    const result = await MyLidarPlugin.startToFSession();
    console.log(result.status);  // ToF session started successfully
  } catch (error) {
    console.error('ToF session error:', error);
  }
}

startToFSession();
```

## Communication Between Plugin and App

The plugin communicates important status updates and error messages directly to your app, using Capacitor's `call.resolve()` and `call.reject()` mechanisms. This ensures that your app can handle the following situations:

- **Permission Denied**: If the user denies camera permissions, the plugin will return an error that you can handle to display a notification or alert to the user.
- **No ToF or LiDAR Support**: If the device doesn’t support ToF (Android) or LiDAR (iOS), the plugin will return an appropriate message so that you can handle the scenario.
- **Depth Data Available**: The plugin returns depth data, which can be used to display or further process in the app.

Here’s an example of how to handle errors and successes in your app:

```typescript
MyLidarPlugin.startLidarSession()
  .then(result => {
    console.log('LiDAR session started:', result.status);
  })
  .catch(error => {
    console.error('Error starting LiDAR session:', error);
    alert('LiDAR is not supported on this device.');
  });
```

## Error Handling

The plugin provides built-in error handling for common scenarios:
- **Permission Denied**: If the user denies camera access, the app will receive an error with a message like `"Camera access denied"`.
- **Unsupported Device**: If the device doesn’t support the required sensor (LiDAR or ToF), the plugin will return an error message that can be shown to the user.
- **Depth Data Unavailable**: If depth data is not available (e.g., due to an unsupported device), the app will receive an error message.

Make sure to handle these cases in your app for a seamless user experience.

## Example App

An example app is available in the `example/` folder of this repository. To run the example app:

1. Install the dependencies:
   ```bash
   npm install
   ```
   
2. Sync the Capacitor project:
   ```bash
   npx cap sync
   ```

3. Open the project in Xcode (for iOS) or Android Studio (for Android) to run it on a real device that supports the required sensors.

## License

MIT License. See `LICENSE` for more information.
