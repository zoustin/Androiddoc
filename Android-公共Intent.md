# Andorid Common Intent

##### 【走出舒适区】学习注重的是一个过程，不要老是盯着目的，而沉思不前行，没有比立刻行动更快达到目的，也不要只盯着脚下忘了方向，走错了路程。人生没有几次重来，要学会享受过程，把过程当作一个结果，体会当中的每一处细节，慢慢的你会发现你达到了你想不到的高度，加油，相信自己。记住学习的旅程很苦闷，当你有所收获时，一定要记得奖赏自己，一定要现在脑海里三问自己，what,why,how? 这样才能化被动为主动学习。这样才是正确的学习方法，学习过后还要不忘复习。最终知识才永远属于你，再一次加油！！

![5b31fc371a9ab](https://i.loli.net/2018/06/26/5b31fc371a9ab.jpg)

###### Android 系统功能的公共意图Intent 👇

### ※Alarm Clock 闹钟

- Action : ACTION_ SET_ ALARM 
- Data URI : None 
- MIME Type: None
- Extras:   附加条件

  | 属性             | 说明                               |
  |:-------------- |:-------------------------------- |
  | EXTRA_HOUR     | 闹铃的小时单位                          |
  | EXTRA_MINUTES  | 闹铃的分单位                           |
  | EXTRA_HOUR     | 闹铃的秒单位                           |
  | EXTRA_DAYS     | 应该使用Calendar日历类指定那天重复响铃，否则为一次性响铃 |
  | EXTRA_RINGTONE | 不指定时，默认使用系统铃声                    |
  | EXTRA_VIBRATE  | True震动   False不震动                |
  | EXTRA_ SKIP_UI | 是否跳过UI界面直接生效                     |
  | EXTRA_MESSAGE  | 自定义消息                            |

  

##### ✅使用案例

```java
/**
     * 设置系统闹钟步骤：
     * 添加权限：<uses-permission android:name="com.android.alarm.permission.SET_ALARM" />
     * 设置相应参数
     * @param message
     * @param hour
     * @param minutes
     */
    public void createAlarm(String message, int hour, int minutes) {
        Intent intent = new Intent(AlarmClock.ACTION_SET_ALARM)
                .putExtra(AlarmClock.EXTRA_MESSAGE, message)
                .putExtra(AlarmClock.EXTRA_HOUR, hour)
                .putExtra(AlarmClock.EXTRA_MINUTES, minutes);
        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        }
    }
```

### ※Timer 计时器

| 属性             | 说明           |
| -------------- | ------------ |
| EXTRA_LENGTH   | 计时器秒的长度      |
| EXTRA_ SKIP_UI | 是否跳过UI界面直接生效 |
| EXTRA_MESSAGE  | 自定义消息        |

```java
     /**
     * 启动倒计时器
     */
    public void startTimer(String message, int seconds) {
        Intent intent = new Intent(AlarmClock.ACTION_SET_TIMER)
                .putExtra(AlarmClock.EXTRA_MESSAGE, message)
                .putExtra(AlarmClock.EXTRA_LENGTH, seconds)
                .putExtra(AlarmClock.EXTRA_SKIP_UI, true);
        if (intent.resolveActivity(getPackageManager()) != null) {
            startActivity(intent);
        }
    }
```
