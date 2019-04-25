> #### Android 全面屏适配

1. **全面屏比例变化：16 : 9 ->18.5 : 9 || 18 : 9**

解决：官方方案，在AndroidManifest.xml中加入,max_aspect的值 默认1.86 ，18.5 ：9的宽高比为 18.5/9 = 2.0555

所以我们需要修改这个值

```java
<meta-data android:name="android.max_aspect"  android:value="ratio_float"/>
//加入这句
<meta-data android:name="android.max_aspect" android:value="2.1"/>
```

或者，在Java代码中设置

```java
public void setMaxAspect() {
        ApplicationInfo applicationInfo = null;
        try {
            applicationInfo = getPackageManager().getApplicationInfo(getPackageName(), PackageManager.GET_META_DATA);
        } catch (PackageManager.NameNotFoundException e) {
            e.printStackTrace();
        }
        if(applicationInfo == null){
            throw new IllegalArgumentException(" get application info = null, has no meta data! ");
        }
        applicationInfo.metaData.putString("android.max_aspect", "2.1");
    }
```

1. **资源图片拉伸问题**

 针对这种问题，我们以分辨率为2160X1080，像素密度为480dpi的VIVO X20Plus手机为例，可以在资源目录下面增加一个文件夹，drawable-h642dp-port-xxhdpi，并将GUI切好的分辨率为2160X1080资源`图片放在这个目录下，系统就能自动使用这种图片，便不会出现拉伸的问题。关于`


