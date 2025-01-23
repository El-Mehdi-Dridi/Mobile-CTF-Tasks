# Guitar 
After decompiling the APK file using JADX , the flag was stored in resources/res/value/String.xml 

![image](https://github.com/user-attachments/assets/3b567c2e-7c00-45be-a09e-9d4f76c208fd)


The flag was encoded with base 64. So i used cyberchef to decode it 

![image](https://github.com/user-attachments/assets/77efafc0-fc0f-4116-8f52-f4501952c407)


```bash
VGhlIGZsYWcgaXM6IGZsYWd7NDZhZmQ0ZjhkMmNhNTk1YzA5ZTRhYTI5N2I4NGFjYzF9Lg==
The flag is: flag{46afd4f8d2ca595c09e4aa297b84acc1}.
```

