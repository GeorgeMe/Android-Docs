---
layout: post
title: "Android Training 01 - 详解Activity生命周期[Lesson 2 - 暂停与恢复activity]"
date: 2012-04-28 15:58
comments: true
sidebar: false
categories: Android
---

* 在使用通常的app时，前端的activity有时候会被其他可见的组件而阻塞(obstructed)，这样会导致当前的activity进入Pause状态。例如，当打开一个半透明的activity时(such as one in the style of a dialog) ，之前的activity会被暂停. 只要这个activity仍然被部分可见，之前的activity则一直处于Paused状态。
* 然而，一旦之前的activity被完全阻塞并不可见，它则会进入Stop状态(which is discussed in the next lesson)。
* 当你的activity进入paused状态，系统会调用你的activity中的onPause()方法, 这个方法会停止目前正在运行的操作，比如暂停视频播放。它会保存那些有可能需要长期保存的信息。(请注意是在onPause里面去保存那些信息)。如果用户从暂停状态回到你的activity，系统会恢复那些数据并执行onResume()方法。
* **Note:**当你的activity接受到调用onPause()信息 ，那可能意味者activity将被暂停一段时间，并且用户很可能回到你的activity。然而，那也是用户要离开你的activtiy的第一个信号。
<!-- more -->
* **Figure 1.**下图显示了，当一个半透明的activity阻塞你的activity时，系统会调用onPause()方法并且这个activity会停留在Paused state (1). 如果用户在这个activity还是在Paused State时回到这个activity，系统则会调用它的onResume() (2).
{% img /images/articles/basic-lifecycle-paused.png %}

### Pause Your Activity[暂停你的Activity]
* 当系统调用你的activity中的onPause(),从技术上讲，那意味着你的activity仍然处于部分可见的状态，当时大多数时候，那意味着用户正在离开这个activity并马上会进入Stopped state. 你通常应该在onPause()回调方法里面做下面的事情:
	* 停止动画或者是其他正在运行的操作，那些都会导致CPU的浪费.
	* 提交没有保存的改变，但是仅仅是在用户离开时期待保存的内容(such as a draft email).
	* 释放系统资源，例如broadcast receivers, sensors (like GPS), 或者是其他任何会影响到电量的资源。
	* 例如, 如果你的程序使用Camera,onPause()会是一个比较好的地方去做那些释放资源的操作。
```java
@Override
public void onPause() {
    super.onPause();  // Always call the superclass method first

    // Release the Camera because we don't need it when paused
    // and other activities might need to use it.
    if (mCamera != null) {
        mCamera.release()
        mCamera = null;
    }
}
```
* 通常，你**not**应该使用onPause()来保存用户改变的数据 (例如填入表格中的个人信息) 到永久磁盘上。仅仅当你确认用户期待那些改变能够被自动保存的时候such as when drafting an email)，你可以把那些数据存到permanent storage 。然而，你应该避免在onPause()时执行CPU-intensive 的工作，例如写数据到DB，因为它会导致切换到下一个activity变得缓慢(你应该把那些heavy-load的工作放到onStop()去做)。
* 如果你的activity实际上是要被Stop，那么你应该为了切换的顺畅而减少在OnPause()方法里面的工作量。
* **Note:**当你的activity处于暂停状态，[Activity](http://developer.android.com/reference/android/app/Activity.html)实例是驻留在内存中的，并且在activity 恢复的时候重新调用。你不需要在恢复到Resumed状态的一系列回调方法中重新初始化组件。

### Resume Your Activity[恢复你的activity]
* 当用户从Paused状态恢复你的activity时，系统会调用onResume()方法。
* 请注意，系统每次调用这个方法时，activity都处于最前台，包括第一次创建的时候。所以，你应该实现onResume()来初始化那些你在onPause方法里面释放掉的组件，并执行那些activity每次进入Resumed state都需要的初始化动作 (例如开始动画与初始化那些只有在获取用户焦点时才需要的组件)
* 下面的onResume()的例子是与上面的onPause()例子相对应的。
```java
@Override
public void onResume() {
    super.onResume();  // Always call the superclass method first

    // Get the Camera instance as the activity achieves full user focus
    if (mCamera == null) {
        initializeCamera(); // Local method to handle camera init
    }
}
```

**学习自：[http://developer.android.com/training/basics/activity-lifecycle/pausing.html](http://developer.android.com/training/basics/activity-lifecycle/pausing.html)，请多指教，谢谢！**  
**转载请注明出自[四方城](http://kesenhoo.github.com)，谢谢配合！**






