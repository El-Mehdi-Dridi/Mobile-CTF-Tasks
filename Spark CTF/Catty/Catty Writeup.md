This is a Mobile CTF task hosted on Spark CTF 2025 

![image](https://github.com/user-attachments/assets/d658f09d-4f4f-4aa3-ad96-c84579865258)


In this task the author give us an apk file and an imge.png as an output 

So I used JADX to decompile the apk file 

In the MainActivity 

![image](https://github.com/user-attachments/assets/7bddf2b5-4a21-428b-84da-6101a773ee74)

I found that there is a function called from the native libriary called named `xorImage`

So I used IDA to decompile the native library 

and this is the result of the decompiled Xor function 

```C
unsigned __int64 __fastcall xor_png_bytes(__int64 a1, __int64 a2, __int64 a3, __int64 a4, unsigned __int64 a5)
{
  const char *v9; // rdx
  unsigned __int64 v10; // rcx
  unsigned __int64 v11; // rdx
  unsigned __int64 v12; // rsi
  unsigned __int64 v13; // rdx
  unsigned __int64 v14; // rdx
  __int64 v15; // rax
  __int64 v16; // rbx
  __int64 (__fastcall **v17)(); // rcx
  __int64 (__fastcall **v19)(); // [rsp+0h] [rbp-178h] BYREF
  _BYTE v20[168]; // [rsp+8h] [rbp-170h] BYREF
  _BYTE v21[152]; // [rsp+B0h] [rbp-C8h] BYREF
  unsigned __int64 v22; // [rsp+148h] [rbp-30h]

  v22 = __readfsqword(0x28u);
  sub_60CA0(&v19, a3, 4LL);
  if ( (v20[(_QWORD)*(v19 - 3) + 24] & 5) != 0 )
  {
    v9 = "Error opening output file: %s";
  }
  else
  {
    if ( a2 )
    {
      if ( a2 == 1 )
      {
        v10 = 0LL;
      }
      else
      {
        v12 = a2 & 0xFFFFFFFFFFFFFFFELL;
        v10 = 0LL;
        do
        {
          if ( (a5 | v10) >> 32 )
            v14 = v10 % a5;
          else
            v14 = (unsigned int)v10 % (unsigned int)a5;
          *(_BYTE *)(a1 + v10) ^= *(_BYTE *)(a4 + v14);
          if ( (a5 | (v10 + 1)) >> 32 )
            v13 = (v10 + 1) % a5;
          else
            v13 = ((int)v10 + 1) % (unsigned int)a5;
          *(_BYTE *)(a1 + v10 + 1) ^= *(_BYTE *)(a4 + v13);
          v10 += 2LL;
        }
        while ( v12 != v10 );
      }
      if ( (a2 & 1) != 0 )
      {
        if ( (a5 | v10) >> 32 )
          v11 = v10 % a5;
        else
          v11 = (unsigned int)v10 % (unsigned int)a5;
        *(_BYTE *)(a1 + v10) ^= *(_BYTE *)(a4 + v11);
      }
    }
    std::ostream::write(&v19, a1, a2);
    v15 = std::filebuf::close(v20);
    v9 = "XOR operation completed. Image saved to: %s";
    if ( !v15 )
    {
      std::ios_base::clear((std::ios_base *)&v20[(_QWORD)*(v19 - 3) - 8], *(_DWORD *)&v20[(_QWORD)*(v19 - 3) + 24] | 4);
      v9 = "XOR operation completed. Image saved to: %s";
    }
  }
  if ( (*(_BYTE *)a3 & 1) != 0 )
    v16 = *(_QWORD *)(a3 + 16);
  else
    v16 = a3 + 1;
  __android_log_print(6LL, "NativeLib", v9, v16);
  v17 = `VTT for'std::ofstream[3];
  v19 = &off_CC200;
  *(_QWORD *)&v20[(_QWORD)*(&off_CC200 - 3) - 8] = v17;
  std::filebuf::~filebuf(v20);
  std::ostream::~ostream(&v19, &`VTT for'std::ofstream[1]);
  std::ios::~ios(v21);
  return __readfsqword(0x28u);
}
```

The apk encrypt each 16 of the image with an hex key  16

Now we should get the key to  decrypt the output.png 

To get the key we must xor the header of the output after getting it with hexdump or any tools to get the header with the png signature `89 50 4E 47 0D 0A 1A 0A`

This is the Python script to get the key 

```Python
import binascii

hex_string = "cc64183f9bb654fa"
png_signature = "89504e470d0a1a0a"

hex_bytes = bytes.fromhex(hex_string)
png_bytes = bytes.fromhex(png_signature)

xor_result = bytes(a ^ b for a, b in zip(hex_bytes, png_bytes))

print(binascii.hexlify(xor_result).decode())
```
the key was : 4534567896bc4ef0

So after getting the key we must xor the output.png with it to get the flag.png 

This is an exemple of python script 

```Python
def xor_image(input_image_path, output_image_path, key):
    key = bytes.fromhex(key)

    with open(input_image_path, 'rb') as f:
        image_data = f.read()

    output_data = bytearray()

    for i in range(0, len(image_data), 16):
        chunk = image_data[i:i + 16]
        chunk_xored = bytearray([byte ^ key[j % len(key)] for j, byte in enumerate(chunk)])
        output_data.extend(chunk_xored)

    with open(output_image_path, 'wb') as f:
        f.write(output_data)

    print(f"XOR operation completed. Output saved as '{output_image_path}'.")

input_image_path = 'output.png' 
output_image_path = 'flag.png'  
key = '4534567896bc4ef0'

xor_image(input_image_path, output_image_path, key)
```

finaly we get it 

![image](https://github.com/user-attachments/assets/4b25005d-1d7e-4bf9-8b7d-a517ed544274)

**SparkCTF{e208d2110981de5687dc1b4d27db41dd}**
