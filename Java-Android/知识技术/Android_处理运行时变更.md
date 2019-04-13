# Android 处理运行时变更

###### 当 Android设备配置发生变化时（例如屏幕方向，键盘可用性）。Android会重启正在运行的Acitvity（先后调用onDestroy()和onCreate() ）。这种重启行为，有时候会给应用恢复大量数据造成高成本，还会给用户留下糟糕的体验。针对这种情况，可以使用下列两种方法解决：

- #### 通过Fragment保留对象

❓方法步骤：

- 扩展Fragment 类并声明对有状态对象的引用
- - - - - 在创建片段后调用setRetaininstance(boolean)
- - - - - 将片段添加到Activity
- - - - - 重启Activity后使用FragmentManager检索片段

✨具体例子：

```java
  public class RetainedFragment extends Fragment {

      // data object we want to retain
      private MyDataObject data;

      // this method is only called once for this fragment
      @Override
      public void onCreate(Bundle savedInstanceState) {
          super.onCreate(savedInstanceState);
          // retain this fragment
          setRetainInstance(true);
      }

      public void setData(MyDataObject data) {
          this.data = data;
      }

      public MyDataObject getData() {
          return data;
      }
  }
```

❗注意：在存储对象的时候，切勿传递与Acitivty绑定的对象，例如，Drawable,Adapter,View获取任何与Context关联的对象，不然它会泄露原始Activity实例的所有视图和资源，无法垃圾回收，丢失大量内存

---

然后呢，使用FragmentManger将片段添加到Activity，在运行变更期间再次启动Activity时，你可以从该片段获取到你存储的数据对象，例如：

```java
public class MyActivity extends Activity {

    private RetainedFragment dataFragment;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        // find the retained fragment on activity restarts
        FragmentManager fm = getFragmentManager();
        dataFragment = (DataFragment) fm.findFragmentByTag(“data”);

        // create the fragment and data the first time
        if (dataFragment == null) {
            // add the fragment
            dataFragment = new DataFragment();
            fm.beginTransaction().add(dataFragment, “data”).commit();
            // load the data from the web
            dataFragment.setData(loadMyData());
        }

        // the data is available in dataFragment.getData()
        ...
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        // store the data in the fragment
        dataFragment.setData(collectMyLoadedData());
    }
}
```

- #### 自行保存对象

因为设备性能限制，而需要避免重启

❓方法步骤：

在文件清单里面的<Activity>添加android:configChanges属性，常用的值包括"orientation"和"keyboardhidden"，分别用于避免因屏幕方向改变和键盘改变而导致的重启

```java
<activity android:name=".MyActivity"
          android:configChanges="orientation|keyboardHidden"
          android:label="@string/app_name">
```

❗注意：在Android 3.2 以上的版本，还应该添加“screenSize”

查看屏幕方向发生变化

```java
@Override
public void onConfigurationChanged(Configuration newConfig) {
    super.onConfigurationChanged(newConfig);

    // Checks the orientation of the screen
    if (newConfig.orientation == Configuration.ORIENTATION_LANDSCAPE) {
        Toast.makeText(this, "landscape", Toast.LENGTH_SHORT).show();
    } else if (newConfig.orientation == Configuration.ORIENTATION_PORTRAIT){
        Toast.makeText(this, "portrait", Toast.LENGTH_SHORT).show();
    }
}
```
