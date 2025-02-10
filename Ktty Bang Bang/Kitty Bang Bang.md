# Kitty Bang Bang 
After decompiling the APK using JADX , in the source code we found this function 
```java
 public static final boolean onCreate$lambda$0(MainActivity this$0, View view, MotionEvent motionEvent) {
        Intrinsics.checkNotNullParameter(this$0, "this$0");
        Log.i("kitty kitty bang bang", "Listening for taps...");
        if (motionEvent.getAction() != 0) {
            return true;
        }
        Log.i("kitty kitty bang bang", "Screen tapped!");
        this$0.showOverlayImage();
        this$0.playSound(R.raw.bang);
        Log.i("kitty kitty bang bang", "BANG!");
        Log.i("kitty kitty bang bang", "flag{" + this$0.stringFromJNI() + '}');
        return true;
    }
```

The flag was stored in logs .

So , we should used “ adb logcat “ to read logs

```bash
PS C:\Users\cat> adb logcat
01-15 09:32:14.890  7132  7132 I kitty kitty bang bang: flag{f9028245dd46eedbf9b4f8861d73ae0f}
01-15 09:32:14.890  7132  7132 I kitty kitty bang bang: Listening for taps...
01-15 09:32:14.896   478  7661 D CCodec  : allocate(c2.android.raw.decoder)
```
