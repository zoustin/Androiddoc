# Android AIDL接口描述语言

##### 【丑话说在前】学习注重的是一个过程，不要老是盯着目的，而沉思不前行，没有比立刻行动更快达到目的，也不要只盯着脚下忘了方向，走错了路程。人生没有几次重来，要学会享受过程，把过程当作一个结果，体会当中的每一处细节，慢慢的你会发现你达到了你想不到的高度，加油，相信自己。记住学习的旅程很苦闷，当你有所收获时，一定要记得奖赏自己，一定要现在脑海里三问自己，what,why,how? 这样才能化被动为主动学习。这样才是正确的学习方法，学习过后还要不忘复习。最终知识才永远属于你，再一次加油！！

![5b31fc371a9ab](https://i.loli.net/2018/06/26/5b31fc371a9ab.jpg)

### ※AIDL : Android 接口描述语言学习

- 问题一：什么是AIDL，它是拿来干嘛的 ？
- 问题二：为什么要用AIDL？
- 问题三：如何使用用AIDL

##### 什么是AIDL?

AIDL全称为(Android Interface Definition Language) 翻译过来就是安卓接口描述语言；是Android平台中实现进程间通讯方式一种，属轻量级通讯机制，有着实现简单、效率较高等优点；Android 系统服务都是通过这种机制封装的。

##### 为什么要使用AIDL？

当你要跟另一个APP进行通信传递数据，就需要使用到AIDL,就比如我们调用第三方登录、支付接口等等，在Android系统中的很多服务，比如短信服务啊也是通过这么方式进入通信的。

##### 如何使用AIDL？

![5b349071348e4](https://i.loli.net/2018/06/28/5b349071348e4.jpg)

###### [改变另一个App的变量]

###### 创建一个AIDL文件 ，里面添加的普通的方法 setData();reBuilde 一下项目

```java
interface IAppService {
    /**
     * Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);
            //修改变量方法
            void setData(String data);
}
```

###### 接着再创建一个AppService 继承Service

```java
public class AppService extends Service {

    public AppService() {
    }
    @Override
    public IBinder onBind(Intent intent) {
        // Binder机制 需要了解一下，这里AIDL 放回的就是一个IBinder对象
        return new IAppService.Stub() {

            @Override
            public void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat, double aDouble, String aString) throws RemoteException {
            }

            @Override
            public void setData(String d) throws RemoteException {
               AppService.this.data =d; //修改数据
            }
        };
    }
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        return super.onStartCommand(intent, flags, startId);
    }
    public String data = "默认数据";
    private boolean runing = true;
    @Override
    public void onCreate() {
        super.onCreate();
        System.out.println("服务启动");
        new Thread(){
            @Override
            public void run() {
                super.run();
              //循环显示值的变化 
                while (runing){
                    try {
                        Thread.sleep(1000); //每一秒打印一次
                        System.out.println(data);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

            }
        }.start();
    }
    @Override
    public void onDestroy() {
        super.onDestroy();
        System.out.println("服务停止");
        runing=false;
    }
}
```

###### 紧接着 New Module ，将之前的AIDL文件整个包目录 复制到这个Moudle 的main目录下，一样reBuilde项目 ,确保两个app的接口一样，包名一样 是com.xxx.xxx.xxx

![5b349379a5a32](https://i.loli.net/2018/06/28/5b349379a5a32.png)

###### 最后在app，绑定服务，对另一个app的数值更改

```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener, ServiceConnection {

    //Android 5.0之后不能通过隐式的方式启动Service ,只能使用显示
    private Intent intentService;

    private EditText editText;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        intentService = new Intent();
        //通过设置包名和类型的方式，也相当于在另外一个app显示启动service
        ComponentName name = new ComponentName("com.example.zoustin.specialist","com.example.zoustin.specialist.AppService");
        intentService.setComponent(name);
        //这种方式，仅仅是启动第三方的服务，还做不到与其进行通信
        findViewById(R.id.btn_startService).setOnClickListener(this);
        findViewById(R.id.btn_stopService).setOnClickListener(this);
        findViewById(R.id.btn_BindService).setOnClickListener(this);
        findViewById(R.id.btn_unBindService).setOnClickListener(this);
        findViewById(R.id.btn_sysData).setOnClickListener(this);

        editText = findViewById(R.id.et_data);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.btn_startService:
                startService(intentService);
                break;
            case R.id.btn_stopService:
                stopService(intentService);
                break;
            case R.id.btn_BindService:
                bindService(intentService,this,Context.BIND_AUTO_CREATE);
                break;
            case R.id.btn_unBindService:
                unbindService(this);
                break;
            case R.id.btn_sysData:

                if(binder != null){
                    try {
                        binder.setData(editText.getText().toString());
                    } catch (RemoteException e) {
                        e.printStackTrace();
                    }
                }
                break;
                default:
                    break;
        }
    }

    private IAppService binder ;
    @Override
    public void onServiceConnected(ComponentName name, IBinder service) {
        System.out.println("服务绑定");
        binder = IAppService.Stub.asInterface(service);
    }

    @Override
    public void onServiceDisconnected(ComponentName name) {
        System.out.println("服务解除");
        binder = null;
    }
}
```
