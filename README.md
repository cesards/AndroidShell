AndroidShell (Mac Os Commands)
============

__MAPS__

Debug KeyStore
```
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

__ADB__

Simulate Application Being Killed
```
// 1 - Exit your app using home button
// 2 - After that
adb shell ps // Find the process id
adb shell ps | grep your.app.package // Then find the line with app name package
adb shell kill -9 21997 // Kill the app by PID
// 3 - Now return to the app using the task switcher
