AndroidShell (Mac Os Commands)
============

<h3>DB</h3>

__DB Getter (Script)__

```
#!/bin/bash
 
# Android 4.3+ changes app's internal directory permissions and you can not just pull your 
# databases to your computer, so I did this as a workaround to extract my databases.
# I only use it for debug, use it under your responsability.
 
package=$1
db_name=$2
path="/data/data/$package/"
 
rm $db_name
adb shell "su -c 'cd $path; chmod -R 777 databases; exit'; exit"
adb pull $path/databases/$db_name
open $db_name
```

<h3>MAPS</h3>

__Debug KeyStore__
```
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

<h3>ADB</h3>

<h4>View connected devices</h4>
```
adb devices
```
If multiple devices are attached, use `adb -s DEVICE_ID` to target a specific device

<h4>Install an application</h4>
```
adb install -r file.apk
// Optional -r argument reinstalls and keeps any data if the application is already installed on the device
```

<h4>Uninstall an application</h4>
```
adb uninstall com.package.name
```
To uninstall the application using uninstall dialog:
```
adb shell am start -a android.intent.action.DELETE -d package:com.package.name
```

<h4>Start an activity</h4>
```
adb shell am start -n com.package.name/.ActivityName
adb shell am start -n com.package.name/com.package.name.ActivityName
```
<h4>Take a screenshot</h4>
```
adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen_name.png
```
Explanation of this command, [here](http://blog.shvetsov.com/2013/02/grab-android-screenshot-to-computer-via.html)

<h4>Power button</h4>
```
adb shell input keyevent 26
adb shell inout text "KEYCODE_POWER"
```
This command sends the power button event to turn the device ON/OFF

<h4>Unlock screen</h4>
```
adb shell input keyevent 82
adb shell inout text "KEYCODE_MENU"
```
This command sends the event that unlocks the lockscreen on the device. It can be combined with the power button command above to turn on and unlock the device
```
adb shell input keyevent 26 82
adb shell inout text "KEYCODE_POWER" "KEYCODE_MENU"
```

<h4>Print all installed packages</h4>
```
adb shell pm list packages -f
```

<h4>Simulate application being killed</h4>
```
// 1 - Exit your app using home button
// 2 - After that
adb shell ps // Find the process id
adb shell ps | grep your.app.package // Then find the line with app name package
adb shell kill -9 21997 // Kill the app by PID
// 3 - Now return to the app using the task switcher
```

<h4>Screen recording using Android 4.4</h4>
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
adb backup --apk com.package.name
// Change the .ab in .tar
dd if=backup.ab bs=24 skip=1 | openssl zlib -d > backup.tar
// Untar  backup.tar
tar xfv backup.tar
// Go in you app private dir
cd apps/com.package.name
```
> You'll need :
> - adb activated
> - physical access to unlocked device
> - works on Nexus 5 at least, might not work with other devices.
> __allowBackup=false will break thos method__

<h4>Use ADB over Wi-Fi without extra application or software</h4>
__Prerequisites__
Android device and computer should be connected in same network.  
* Connect Android device with USB cable to computer
* Use following command in terminal to make sure adb is running in USB mode.
```
$adb usb
// Restarting in USB mode
// Connect to the device over USB.
```

* Execute following command in terminal to make sure adb identify/list gets connected with the device.
```
$adb devices
```

* Change adb mode from USB to tcpip using following command. 
```
$adb tcpip 5555
// Restarting in TCP mode port: 5555
```

* Now, adb is running over TCP/IP mode, Let’s find IP address of Android device. Go to Settings in Android device -> About -> Status -> IP address. note down the IP address of connected Android Device.
* Use following command to connect ADB with IP address
```
$adb connect #.#.#.# 
// Connected to #.#.#.#:5555
```

* Now adb is working over Wi-fi, You can remove USB cable from Android device.
* To confirm adb is working over Wi-fi and your device is still connect. you can use following command
```
$adb devices
#.#.#.#:5555 device
```

You’re now ready to go!, Enjoy ADB over Wi-fi.
Use following command to change ADB mode to USB
```
$adb usb
```

<h4>Filter by tagname in logcat</h4>
```
adb logcat -s TAG_NAME
adb logcat -s TAG_NAME_1 TAG_NAME_2
```

<h4>Filter by priority in logcat</h4>
```
adb logcat "*:<priority>"
// Where <priority> can be V (Verbose), D (Debug), I (Info), W (Warning), E (Error), F (Fatal), S (Silent)
```
It can be combined with tagname command, to filter by tagname and priority
```
adb logcat -s TEST: W
```

<h4>Filter using grep in logcat</h4>
```
adb logcat | grep "term"
adb logcat | grep "term1\|term2"
````

<h4>See the executed SQL statements in plain text in logcat</h4>
```
adb shell setprop log.tag.SQLiteLog V
adb shell setprop log.tag.SQLiteStatements V
adb shell stop
adb shell start
```
That's it. Whenever any of the installed apps now accesses the database you should see the executed statement in the log output.

<h4>Testing - Execute Monkey to test user interaction</h4>

The Monkey is a program that runs on your emulator or device and generates pseudo-random streams of user events such as clicks, touches, or gestures, as well as a number of system-level events. You can use the Monkey to stress-test applications that you are developing, in a random yet repeatable manner.

```
adb shell monkey [options] <event-count>
// Basic, make 500 random actions
adb shell monkey -p your.package.name -v 500 
```
Complete information at http://developer.android.com/tools/help/monkey.html




