## aapt-apktool_v28
aapt custom version base apktool prebuild version

### Why I build the aapt version.
I build this aapt version to build the resource xml which contains non-printable utf-8 chars.
for example:
```
<o.ᗮ android:gravity="center_vertical" android:layout_width="wrap_content" android:layout_height="wrap_content" app:divider="?actionBarDivider" app:dividerPadding="12.0dip"
  xmlns:android="http://schemas.android.com/apk/res/android" xmlns:app="http://schemas.android.com/apk/res-auto" />
```
the char after "o." is non-printable code, the file is created by the third Obfuscator tool.
the aapt from ADK is not support this kind of file(the expat lib not support this kind of xml file actually).
The current apktool is released with a prebulid aapt version to support some feature, and I have to build the aapt base on apktool version.

### where the code from?
The code build on the base: https://github.com/go0517/aapt-3

And Add the change code from: https://github.com/iBotPeaches/platform_frameworks_base 

At last update some code from android-v24 to android-v28

### what I changed?
1. the function utf8_isName2 and utf8_isName3 in xmltok.c in libexpat
```
static int PTRFASTCALL
utf8_isName2(const ENCODING *enc, const char *p)
{
  int ret = ((p[0] & 0xE0) == 0xC0) && ((p[1] & 0xC0) == 0x80);
  printf("___utf8_isName2____RET=%d, utf8byte:0X%02X%02X\n", ret,
         (unsigned char)(p[0]), (unsigned char)(p[1]));

  return ret; //UTF8_GET_NAMING2(namePages, (const unsigned char *)p);
}

static int PTRFASTCALL
utf8_isName3(const ENCODING *enc, const char *p)
{
  int ret = ((p[0] & 0xF0) == 0xE0) && ((p[1] & 0xC0) == 0x80) && ((p[2] & 0xC0) == 0x80);
  printf("___utf8_isName3____RET=%d, utf8byte:0X%02X%02X%02X\n", ret,
         (unsigned char)(p[0]), (unsigned char)(p[1]), (unsigned char)(p[2]));
  return ret; //UTF8_GET_NAMING3(namePages, (const unsigned char *)p);
}
```
only check whether it is a valid utf-8 char.

I alse change the utf8_isNmstrt2 and utf8_isNmstrt3 function, but I think it's not necessary.

2. comment the code use the var classIdentChars in Resource.cpp
   the commented code check whether the class name only contains char in "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ._0123456789$", because now the class name contains the utf-8 char.
   
3. some config of code block project.
   I am a beginer of code block. so please read the guide in "https://github.com/go0517/aapt-3" for build the project first.


