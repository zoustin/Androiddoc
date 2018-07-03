# Android 四大组件

##### 【走出舒适区】学习注重的是一个过程，不要老是盯着目的，而沉思不前行，没有比立刻行动更快达到目的，也不要只盯着脚下忘了方向，走错了路程。人生没有几次重来，要学会享受过程，把过程当作一个结果，体会当中的每一处细节，慢慢的你会发现你达到了你想不到的高度，加油，相信自己。记住学习的旅程很苦闷，当你有所收获时，一定要记得奖赏自己，一定要现在脑海里三问自己，what,why,how? 这样才能化被动为主动学习。这样才是正确的学习方法，学习过后还要不忘复习。最终知识才永远属于你，再一次加油！！

![5b31fc371a9ab](https://i.loli.net/2018/06/26/5b31fc371a9ab.jpg)

### ※Activity活动

##### 1. Activity的生命周期：6个系统回调方法

onCreate() :此时Activity已经创建但还在后台，为不可见状态

onStart()：此时Activity出现在前台，为可见状态，但还是操作状态

onResume()：此时Activity出现在前台，为可见状态，并且可操作

onPause()：此时Activity的焦点被其他控件获取，比如对话框 

onStop()：此时Activity出任务栈，被其他Activity占领 

onDestroy()：此时Activity被系统销毁还有一个

onRestart()：当Activity跳转到另外一个Activity，在没有销毁的情况下，返回出现，或者按home键，再回到该Activity

###### ![](https://developer.android.com/guide/components/images/activity_lifecycle.png)一个保持状态的案例

```java
TextView mTextView;

// some transient state for the activity instance
String mGameState;

@Override
public void onCreate(Bundle savedInstanceState) {
    // call the super class onCreate to complete the creation of activity like
    // the view hierarchy
    super.onCreate(savedInstanceState);

    // recovering the instance state
    if (savedInstanceState != null) {
        mGameState = savedInstanceState.getString(GAME_STATE_KEY);
    }

    // set the user interface layout for this activity
    // the layout file is defined in the project res/layout/main_activity.xml file
    setContentView(R.layout.main_activity);

    // initialize member TextView so we can manipulate it later
    mTextView = (TextView) findViewById(R.id.text_view);
}

// This callback is called only when there is a saved instance that is previously saved by using
// onSaveInstanceState(). We restore some state in onCreate(), while we can optionally restore
// other state here, possibly usable after onStart() has completed.
// The savedInstanceState Bundle is same as the one used in onCreate().
@Override
public void onRestoreInstanceState(Bundle savedInstanceState) {
    mTextView.setText(savedInstanceState.getString(TEXT_VIEW_KEY));
}

// invoked when the activity may be temporarily destroyed, save the instance state here
@Override
public void onSaveInstanceState(Bundle outState) {
    outState.putString(GAME_STATE_KEY, mGameState);
    outState.putString(TEXT_VIEW_KEY, mTextView.getText());

    // call superclass to save any view hierarchy
    super.onSaveInstanceState(outState);
}
```

##### 2.<Activity>几个主要的属性：

| mainfest 属性           | 说明                                                                                                                        |
| --------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| taskAffinity          |                                                                                                                           |
| launchMode            |                                                                                                                           |
| allowTaskOnLaunch     |                                                                                                                           |
| alwaysRetainTaskState | 如果该属性在任务的根活动中设置为“true”，则不会发生刚才描述的默认行为。任务在它的堆栈中保留所有的活动，即使经过很长一段时间。                                                         |
| finishOnTaskLaunch    | 这个属性类似于clearTaskOnLaunch，但是它操作的是一个活动，而不是一个完整的任务。它还会导致任何活动消失，包括根活动。当设置为“true”时，该活动仅在当前会话中是任务的一部分。如果用户离开，然后返回到任务，那么它就不再存在了。 |
| clearTaskonLaunch     | 如果该属性在任务的根活动中被设置为“true”，那么每当用户离开任务并返回该任务时，堆栈就会被清除到根活动中。换句话说，它是永远保持任务状态的反义词。用户总是以其初始状态返回任务，即使只离开任务一分钟。                     |

| Intent flags 属性                | 说明              |
| ------------------------------ | --------------- |
| FLAG _  ACTIVITY _  NEW_TASK   | 跟SingleTask属性类型 |
| FLAG_ ACTIVITY _  CLEAR _ TOP  |                 |
| FLAG_ ACTIVITY _  SINGLE _ TOP | 跟SingleTop属性类似  |

##### 3.启动模式

standard(标准模式):可以多次被实例化，在同一个任务栈中可以存在多个Activity实例

singleTop(栈顶模式):只有栈顶的时候，才会通过onNewIntent() 调用原来的实例，否则它也会创建新的实例

singleTask(单任务模式):如果该实例已经存在，但不在栈顶，它会销毁它上层的所有Activity,

                                     如果不存在，就新开一个任务栈，将它放到栈顶；

singleInstance(单例模式): 独占一个任务栈，在整个系统中只有一个实例，不管有多少个栈；

###### 如何更改启动模式，有以下两种方式

第一种：使用清单文件manifest file 在属性设置 launchMode:"standard"

第二种：使用Intent Flags 

##### 4.进程：

前台进程：与用户交互的Activity(onResume()方法被调用)

                   正在执行onReceiver()方法的BoradcastReceiver

                   正在执行Service的生命周期，例如，onCreate(),onStart(),onDestroy()

可见进程：正在显示对话框的Activity调用onPasue

                   绑定在前台Activity的服务Service.startForeground()

                  它正在托管一个服务，该服务系统正在使用用户所知道的特定功能，如实时壁纸、输入方法服务等。

服务进程：正在运行已调用startService()方法启动的服务并且不属于上述两个更高级别进程的进程，或者说正在执行后台播放音乐或者从网络下载数据，除非内存不足，否则系统会一直让服务进程保存运行状态



缓存进程：不含有任何活动组件的进程，保留这种进程的唯一目的是为了起缓存，已缩短下次运行组件所需要的启动时间。为使总体系统资源在进程缓存和底层内核缓存之间保持平衡。

##### 5.Fragment片段：

![](https://developer.android.com/images/fragment_lifecycle.png)

什么是片段？

Fragment表示Activity中的行为或者用户界面部分，你可以是使用多个片段组合在一个Activity中构建多窗格

它也具有自己的生命周期，不过它依附在Activity上，会伴随中Activity的诞生到死亡



为什么要使用片段？

Fragment的使用，是为了给如今大屏幕手机、平板电脑更加灵活额UI设计，解决屏幕碎片化问题。



Fragment的生命周期怎么看？

如上图，通常你至少需要实现以下的生命周期方法：onCreate() 、onCreateView()、onPause()



如何在Actvity中添加布局？

有两种方式：

第一种：在布局文件中的android:name 声明要实例的Fragment类,当系统创建此Activity布局时，会实例化在布中指定的每一个片段，并为每一个片段调用onCrateView()方法，已检索每一个片段的布局。系统会直接插入片段返回的view来替代<fragment>元素

第二种：通过Java代码将片段添加到某个现有的VIewGroup

这时候必须使用到FragmentTransaction ，如，在Actvity中获取一个FragmentTransaction实例

```java

FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
```

然后，通过add()方法添加片段，指定要添加的片段以及将其插入到那个视图

```java
ExampleFragment fragment = new ExampleFragment();
fragmentTransaction.add(R.id.fragment_container, fragment);
fragmentTransaction.commit();
```

管理Fragment:

通过findFragmentById(对于在Activity布局中提供的UI的片段) 或 findFragmentByTag() (对于提供或不提供UI的片段)

通过popBackStack() 将事务添加到事务返回栈

通过addOnBackStackChangedListener()监听返回栈

执行片段：

获取实例

```java
FragmentManager fragmentManager = getFragmentManager();
FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
```

替换片段

```
// Create new fragment and transaction
Fragment newFragment = new ExampleFragment();
FragmentTransaction transaction = getFragmentManager().beginTransaction();

// Replace whatever is in the fragment_container view with this fragment,
// and add the transaction to the back stack
transaction.replace(R.id.fragment_container, newFragment);
transaction.addToBackStack(null); //允许用户通过返回按钮返回上一片段

// Commit the transaction
transaction.commit();
```
