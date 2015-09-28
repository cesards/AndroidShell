AndroidShell (Mac Os Commands)
=============================

Summary:
<br>
* <a href="#sha1">SHA1<br>
* <a href="#adb">ADB<br>
* <a href="#db">Database<br>
<br>

<a name="sha1">### SHA1</a>
In order to get SHA1 to use it in many services, like Google+ Sign In, Maps, In app purchases, we should generate keys for every keystore (certificate):

__Debug KeyStore__
```sh
$ keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
# or
$ keytool -list -v -keystore {path_to_keystore}/debug.keystore -alias androiddebugkey -storepass android -keypass android
```
__Release KeyStore__
```sh
$ keytool -list -v -keystore {path_to_keystore}/my-release.keystore -alias {alias_name} -storepass {store_pass} -keypass {keypass}
```

<a name="adb">
### ADB</a>

<h4>Watching StrictMode</h4>

If you’re using `penaltyLog()`, the default, just run 
```sh
adb logcat 
```
and watch the terminal output. Any violations will be logged to your console, slightly rate-limited for duplicate elimination.

If you want to get fancier, turn on `penaltyDropbox()` and they’ll be written to the DropBoxManager, where you can extract them later with
```sh
adb shell dumpsys dropbox data_app_strictmode --print
```

<h4>View connected devices</h4>
```sh
$ adb devices
```
If multiple devices are attached, use `adb -s DEVICE_ID` to target a specific device

<h4>Install an application</h4>
```sh
$ adb install -r file.apk
# optional -r argument reinstalls and keeps any data if the application is already installed on the device
```

<h4>Uninstall an application</h4>
```sh
$ adb uninstall com.package.name
```
To uninstall the application using uninstall dialog:
```sh
$ adb shell am start -a android.intent.action.DELETE -d package:com.package.name
```

<h4>Start an activity</h4>
```sh
$ adb shell am start -n com.package.name/.ActivityName
$adb shell am start -n com.package.name/com.package.name.ActivityName
```
<h4>Take a screenshot</h4>
```sh
$ adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > screen_name.png
```
Explanation of this command, [here](http://blog.shvetsov.com/2013/02/grab-android-screenshot-to-computer-via.html)

<h4>Power button</h4>
```sh
$ adb shell input keyevent 26
$ adb shell inout text "KEYCODE_POWER"
```
This command sends the power button event to turn the device ON/OFF

<h4>Unlock screen</h4>
```sh
$ adb shell input keyevent 82
$ adb shell inout text "KEYCODE_MENU"
```
This command sends the event that unlocks the lockscreen on the device. It can be combined with the power button command above to turn on and unlock the device
```sh
$ adb shell input keyevent 26 82
$ adb shell inout text "KEYCODE_POWER" "KEYCODE_MENU"
```

<h4>Print all installed packages</h4>
```sh
$ adb shell pm list packages -f
```

<h4>Simulate application being killed</h4>
```sh
# exit your app using home button
# after that
$ adb shell ps // Find the process id
$ adb shell ps | grep your.app.package // Then find the line with app name package
$ adb shell kill -9 21997 // Kill the app by PID
# now return to the app using the task switcher
```

<h4>Screen recording using Android 4.4</h4>
```sh
$ adb shell screenrecord --verbose /sdcard/nexus5.mp4 // Basic recording from shell
# press Ctrl-C to stop
$ screenrecord --verbose --time-limit 30 /sdcard/nexus5.mp4 // Recording for 30 seconds
$ screenrecord --verbose --bit-rate 8000000 --time-limit 30 /sdcard/nexus5.mp4 // Recording for 30 seconds with 8Mbps bitrate
$ screenrecord --verbose --rotate /sdcard/nexus5.mp4 // Record in portrait view / horizontal
```

<h4>Retrieve application's private data and databases for non debug application without root access</h4>
```sh
# get a backup of your application data
$ adb backup --apk com.package.name
# Change the .ab in .tar
$ dd if=backup.ab bs=24 skip=1 | openssl zlib -d > backup.tar
# untar  backup.tar
tar xfv backup.tar
# go in you app private dir
$ cd apps/com.package.name
```
> You'll need :
> - adb activated
> - physical access to unlocked device
> - works on Nexus 5 at least, might not work with other devices.
> __allowBackup=false will break thos method__

<h4>Check Battery Stats</h4>
```sh
$ adb shell dumpsys batterystats
# global and per-UID statistics
# options: --help
```

<h4>Auto Backup Data (only in Android M)</h4>
```sh
$ adb shell bmgr

$ adb shell bmgr run
# do some work in the app
$ adb shell bmgr fullbackup com.domain.android.app_name
# uninstall and reinstall the app
$ adb shell bmgr restore com.domain.android.app_name
```

<h4>Simulate fingerprint inputs (only in Android M)</h4>
```sh
$ adb -e emu finger touch 1
```

<h4>Use ADB over Wi-Fi without extra application or software</h4>
__Prerequisites__
Android device and computer should be connected in same network.  
* Connect Android device with USB cable to computer
* Use following command in terminal to make sure adb is running in USB mode.
```sh
$ adb usb
# restarting in USB mode
# connect to the device over USB.
```

* Execute following command in terminal to make sure adb identify/list gets connected with the device.
```sh
$ adb devices
```

* Change adb mode from USB to tcpip using following command. 
```sh
$ adb tcpip 5555
# restarting in TCP mode port: 5555
```

* Now, adb is running over TCP/IP mode, Let’s find IP address of Android device. Go to Settings in Android device -> About -> Status -> IP address. note down the IP address of connected Android Device.
* Use following command to connect ADB with IP address
```sh
$ adb connect #.#.#.# 
# connected to #.#.#.#:5555
```

* Now adb is working over Wi-fi, You can remove USB cable from Android device.
* To confirm adb is working over Wi-fi and your device is still connect. you can use following command
```sh
$ adb devices
*.*.*.*:5555 device
```

You’re now ready to go!, Enjoy ADB over Wi-fi.
Use following command to change ADB mode to USB
```sh
$ adb usb
```

<h4>Filter by tagname in logcat</h4>
```sh
$ adb logcat -s TAG_NAME
$ adb logcat -s TAG_NAME_1 TAG_NAME_2
```

<h4>Filter by priority in logcat</h4>
```sh
$ adb logcat "*:<priority>"
// Where <priority> can be V (Verbose), D (Debug), I (Info), W (Warning), E (Error), F (Fatal), S (Silent)
```
It can be combined with tagname command, to filter by tagname and priority
```sh
$ adb logcat -s TEST: W
```

<h4>Filter using grep in logcat</h4>
```sh
$ adb logcat | grep "term"
$ adb logcat | grep "term1\|term2"
````

<h4>See the executed SQL statements in plain text in logcat</h4>
```sh
$ adb shell setprop log.tag.SQLiteLog V
$ adb shell setprop log.tag.SQLiteStatements V
$ adb shell stop
$ adb shell start
```
That's it. Whenever any of the installed apps now accesses the database you should see the executed statement in the log output.

<h4>Testing - Execute Monkey to test user interaction</h4>

The Monkey is a program that runs on your emulator or device and generates pseudo-random streams of user events such as clicks, touches, or gestures, as well as a number of system-level events. You can use the Monkey to stress-test applications that you are developing, in a random yet repeatable manner.

```sh
$ adb shell monkey [options] <event-count>
# basic, make 500 random actions
$ adb shell monkey -p your.package.name -v 500 
```
Complete information at http://developer.android.com/tools/help/monkey.html

<h4>Find out processor version on Android Device (check if it's an ARM, for example)</h4>
```sh
$ adb shell cat /proc/cpuinfo
```

<h4>Indentify Frame Rate Issues (Dumpsys)</h4>
```sh
$ cd platform-tools/
# before executing next command, go to Settings --> Developer Options --> Enable Profile GPU rendering option
# and make sure to kill your application first or at least to kill the window that you want to profile, and then you run the command:
$ adb shell dumpsys gfxinfo
```
Purpose of Dumpsys is identifying frame rate issues and fix them.

What matter is the three columns shown. Copy paste results in a spreadsheet. And you will get a result like this one:

![Image](./images/dumpsys_sample.png)

This is the data you can grab. You can create a stack graph, so every bar contains the sum of the three columns on the left in the data we output. Is the time it takes to update the display list on every frame. 

* The middle column is called process display list. It's the time we take to draw the actual display list
* The last column is the time we take to swap the buffers, so to give the buffer back to surface flinger. Scrolling or doing any kind of actions should be below 16 millisecond limit. So this app is running at 60FPS, we're vsync'd, everything is going great. You can see that most of the time, you should spend most of the time in process display list, so drawing, executing the display list should be where you spend the bulk of the time.
* The blue part is your code. When you write your Java code, your `onDraw()` method, and this is where you can do most of the optimizations

There is a distinction between the GPU composition with frame buffers and overlays. So you can use a tool called __dumpsys surface flinger__ to see the state of overlays and frame buffers in the system.
```sh
$ adb shell dumpsys SurfaceFlinger
```

You're going to get a list with all the windows currently visible on the screen.

More info about this tool, [here](https://www.youtube.com/watch?v=Q8m9sHdyXnE#t=2469)

<a name="db">
### Database</a>

__DB Getter (Script)__ [by Ignasi](https://gist.github.com/ignasi)

```sh
#!/bin/bash
 
# android 4.3+ changes app's internal directory permissions and you can not just pull your 
# databases to your computer, so this is a workaround to extract your databases.
# I only use it for debug, use it under YOUR responsability. IT REQUIRES ROOT
 
package=$1
db_name=$2
path="/data/data/$package/"
 
rm $db_name
adb shell "su -c 'cd $path; chmod -R 777 databases; exit'; exit"
adb pull $path/databases/$db_name
open $db_name
```


