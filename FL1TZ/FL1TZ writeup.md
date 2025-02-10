# FLT1TZ

The first thing we must do is change the extension to .apk, then we should install it on our emulator.

![image](https://github.com/user-attachments/assets/c85a0dd9-73d7-453f-95cf-9cb186f88eea)


So we open the APK file with JADX to read the source code and other files, and start our static analysis. The first thing we analyze is the AndroidManifest.xml.

```xml
<activity
            android:name="io.fl1tz.ctf.MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
                <category android:name="android.intent.category.LAUNCHER"/>
            </intent-filter>
        </activity>
        <activity android:name="io.fl1tz.ctf.FlagActivity"/>
        <activity
            android:name="io.fl1tz.ctf.UnreachableActivity"
            android:exported="false"/>
```

So we try to start the `io.fl1tz.ctf.FlagActivity` activity using ADB.

```bash
PS C:\Users\mahdi> adb root
restarting adbd as root
PS C:\Users\mahdi> adb shell am start -n io.fl1tz.ctf/.FlagActivity
Starting: Intent { cmp=io.fl1tz.ctf/.FlagActivity }
```

![image 1](https://github.com/user-attachments/assets/5bb7a18d-b8d6-4b2c-8a27-80035725db17)


We guess that we got the flag, but after decoding those symbols, we found a string.

```bash
unreachable
```

So we try to start `io.fl1tz.ctf.UnreachableActivity`. With this step, we proceed to more steps.

![image 2](https://github.com/user-attachments/assets/ab37fa03-7909-4748-9884-ec97cc7d868c)


***can you pass it to the native fuction***

We have to read The **Native Function** first. We found this suspicious line

```java
String ok = InternetUtil.getKey("fakekeyfakekeyfakekey");
```

JADX doesn't allow us to modify the source code, so we use apktool.

```java
apktool d .\FL1TZ.apk
```

We use this command to find the activity.

```powershell
Get-ChildItem -Path C:\Users\mahdi\Downloads\FL1TZ\FL1TZ\ -Filter "Unreacha*" -Recurse -ErrorAction SilentlyContinue
```

After we find it, we modify the key in the smali code.

```
.line 35
    const-string v3, "wohoo_you_found_the_key"
```

After that, we must rebuild the APK and sign it with these commands.

```
apktool b -o  .\FL1TZ.apk .\FL1TZ
java -jar .\uber-apk-signer-1.3.0.jar -a .\FL1TZ\FL1TZ.apk --out .\ctfiTask
```

After installing it on the emulator, we must re-read the source code. We found that 'topsecret' is outputted in the logs.

```
log.i("topsecret:", newKey.substring(0, newKey.length() - 4) + "}")
```

We should analyse the logs with 

```powershell
adb logcat
```

![image 3](https://github.com/user-attachments/assets/d8ce9b6f-e82e-4615-ace0-d36d05abc9f4)


We get it 

~~FL1TZ{Gl0ub_gra33_ft0ur_Sbe7}~~
