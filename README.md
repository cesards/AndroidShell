AndroidShell (Mac Os Commands)
============

<h3>MAPS</h3>

__Debug KeyStore__
```
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

<h3>ADB</h3>

<h4>Simulate Application Being Killed<h4
```
// 1 - Exit your app using home button
// 2 - After that
adb shell ps // Find the process id
adb shell ps | grep your.app.package // Then find the line with app name package
adb shell kill -9 21997 // Kill the app by PID
// 3 - Now return to the app using the task switcher
```

<h4>Screen Recording using Android 4.4</h4>
```
adb shell screenrecord --verbose /sdcard/nexus5.mp4 // Basic recording from shell
// Press Ctrl-C to stop
screenrecord --verbose --time-limit 30 /sdcard/nexus5.mp4 // Recording for 30 seconds
screenrecord --verbose --bit-rate 8000000 --time-limit 30 /sdcard/nexus5.mp4 // Recording for 30 seconds with 8Mbps bitrate
screenrecord --verbose --rotate /sdcard/nexus5.mp4 // Record in portrait view / horizontal
```

<h4>Retrieve application's private data and databases for non debug application without root access</h4>
```
// Get a backup of your application data
adb backup --apk <package_name>
// Change the .ab in .tar
dd if=backup.ab bs=24 skip=1 | openssl zlib -d > backup.tar
// Untar  backup.tar
tar xfv backup.tar
// Go in you app private dir
cd apps/<package_name>
```
> You'll need :
> - adb activated
> - physical access to unlocked device
> - works on Nexus 5 at least, might not work with other devices.
> __allowBackup=false will break thos method__

<h4>Use ADB over Wi-Fi without extra application or software<h4>
__Prerequisites__
Android device and computer should be connected in same network.  
1. Connect Android device with USB cable to computer
2. Use following command in terminal to make sure adb is running in USB mode.
```
$adb usb
// Restarting in USB mode
// Connect to the device over USB.
```

3. Execute following command in terminal to make sure adb identify/list gets connected with the device.
```
$adb devices
```

4. Change adb mode from USB to tcpip using following command. 
```
$adb tcpip 5555
// Restarting in TCP mode port: 5555
```

5. Now, adb is running over TCP/IP mode, Let’s find IP address of Android device. Go to Settings in Android device -> About -> Status -> IP address. note down the IP address of connected Android Device.
6. Use following command to connect ADB with IP address
```
$adb connect #.#.#.# 
// Connected to #.#.#.#:5555
```

7. Now adb is working over Wi-fi, You can remove USB cable from Android device.
8. To confirm adb is working over Wi-fi and your device is still connect. you can use following command
```
$adb devices
#.#.#.#:5555 device
```

You’re now ready to go!, Enjoy ADB over Wi-fi.
Use following command to change ADB mode to USB
```
$adb usb
```
