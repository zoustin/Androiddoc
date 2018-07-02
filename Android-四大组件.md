# Android 四大组件

##### 【走出舒适区】学习注重的是一个过程，不要老是盯着目的，而沉思不前行，没有比立刻行动更快达到目的，也不要只盯着脚下忘了方向，走错了路程。人生没有几次重来，要学会享受过程，把过程当作一个结果，体会当中的每一处细节，慢慢的你会发现你达到了你想不到的高度，加油，相信自己。记住学习的旅程很苦闷，当你有所收获时，一定要记得奖赏自己，一定要现在脑海里三问自己，what,why,how? 这样才能化被动为主动学习。这样才是正确的学习方法，学习过后还要不忘复习。最终知识才永远属于你，再一次加油！！

![5b31fc371a9ab](https://i.loli.net/2018/06/26/5b31fc371a9ab.jpg)

### ※Activity活动

1. Activity的生命周期：6个系统回调方法 

   onCreate() : 此时Activity已经创建但还在后台，为不可见状态

   onStart()：此时Activity出现在前台，为可见状态，但还是操作状态

   onResume()：此时Activity出现在前台，为可见状态，并且可操作

   onPause()：此时Activity的焦点被其他控件获取，比如对话框

    onStop()：此时Activity出任务栈，被其他Activity占领

    onDestroy()：此时Activity被系统销毁

   还有一个onRestart()：当Activity跳转到另外一个Activity，在没有销毁的情况下，返回出现，或者按home键，再回到该Activity

   ![](https://developer.android.com/guide/components/images/activity_lifecycle.png)

   一个保持状态的案例

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

