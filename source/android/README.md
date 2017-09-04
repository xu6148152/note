## Android基础
### Android体系架构
APP->Framework->Native Library, AndroidRuntime(ART)->Hal->Linux Kernal

### Activity
#### launchMode
normal, singleTop, singleInstance, singleTask
normal: 默认模式
singleTop: 当顶上Activity相同时，不重复创建，顶部onNewIntent调用
singleInstance: 不允许别的ACTIVITY在其TASK中，存在onNewIntent调用
singleTask: 允许别的ACTIVITY在其TASK中，与taskAffinity配合使用

Activity启动:
Instrumentation execStartActivitiesAsUser -> ActivityManagerNative startActivities -> Binder START_ACTIVITY_TRANSACTION -> 响应结果 -> ActivityThread sendActivityResult -> H handler LAUNCH_ACTIVITY -> handleLaunchActivity -> performLaunchActivity

### 进程优先级
前台进程:
 * Activity用户正在交互4
 * BroadcastReceiver onReceive正在执行
 * Service 生命周期正在执行
可视进程:
 * Activity可见，但onPause已经调用，如果弹窗的情况
 * Service以startForground()方式调用
 * Service正在使用系统的特定功能，如壁纸，输入法
服务进程:
 * 普通的服务都是
缓存进程:(后台进程，空进程)
 * Activity退到后台,onStop已经执行，系统会采用LRU方式进行回收

### Loaders
简化线程管理，缓存结果，可以监控数据变化

### Service
startService: onCreate()->onStartCommand()->onDestroy()
bindService: onCreate()->onBind()->onUnbind()->onDestroy()
onStartCommand()返回值表示系统是否能够杀掉服务
START_NOT_STICKY:
如果服务，被系统杀掉.除非有未接收的intents，否则不重新创建服务
START_STICKY:
如果服务被系统杀掉，重新创建服务
START_REDELIVER_INTENT:
重新创建服务，并接收之前最后的指令

以上指令都能通过startService重新创建，通过stopService或者stopSelf销毁服务

绑定服务
主要用于ipc，进程间通讯，多个组件可以同时绑定同一个服务，调用`unbindservice`解除绑定，如果没有服务绑定了，那么系统会销毁。广播不能bindService
步骤:
 * 实现`ServiceConnection`
 * 调用`bindService`传入`ServiceConnection`
 * 系统回调`onServiceConnected`
 
提供IBinder实现
 * 继承Binder
 * 使用Messanger(使用AIDL技术)
 * AIDL

AIDL
定义AIDL接口
* 创建.aidl文件
* 实现接口
* 客户端复写`onBind`返回`Stub`类

### BroadcastReceiver
LocalBroadcastReceiver同一客户端内的广播

### ContentProvider
数据访问接口，可以监控数据变化

### RenderScript
加速计算

### SurfaceView
两个个surface

### Handler
Looper, MessageQueue属于Looper

### 动画主流程
ObjectAnimator.start()
-> ValueAnimator.start()
-> animationHandler.start()
-> AnimationHandler.scheduleAnimation()
-> Choreographer.postCallback()
-> postCallbackDelayed()
-> postCallbackDelayedInternal()
-> scheduleFrameLocked()
-> scheduleVsyncLocked()
-> DisplayEventReceiver.scheduleVsync()
-> onVsync()
-> doFrame(）
-> doCallbacks()
-> animationHandler.run()
-> doAnimationFrame()
-> animationFrame()
-> animateValue()

### AsyncTask
线程池128，同时运行CPU*2 + 1，futureTask + Handler, 支持串行执行和并行执行，默认是串行执行
生命周期不好控制,可能会存在内存泄露的情况。取消执行不一定能够成功。

### 多线程
线程池: newSingleThreadExecutor, newCachedThreadPool, newFixedThreadPool, newScheduledThreadPool,newWorkStealingPool(1.8)



### 性能优化
#### ANR
* 5s没有响应用户输入事件
* 10s内广播接收器没有处理完
* 20s内服务没有处理完
分析, Strict Mode
/data/anr/traces.txt 


Systrace, TraceView分析程序瓶颈
TraceView 查看线程运行时间
timeline panel
profile panel
Debug.startMethodTracking()
默认放在存储卡的根目录，名字叫dmtrace.trace
dmtracedump工具

ui性能 SysTrace 查看组件渲染时间，分析警告
Track.beginSection()

HeapViewer
堆分配查看

Allocation Tracker

内存泄露分析
Context泄露，导致Activity无法释放

#### 布局优化
避免使用Relative或者LinearLayout的weight，可以使用ConstraintLayout
#### 渲染优化
UI分两个阶段，UI线程负责记录VIEW以及绘制，渲染线程用于DrawFrame
UI线程:
耗时操作放入后台线程，解码操作放入后台线程
#### 渲染线程
避免使用Canvas.saveLayer()
对大量的Path做动画
避免使用Cavans.clipPath()，可以使用Shader替代
#### 电池优化
有关CPU,广播，屏幕的操作，需要多考虑
善用缓存
任务批量处理
平台特性
JobScheduling
Battery Historian

收集网络流量数据
用户发起的网络请求
app自动发起的网络请求
服务端发起的网络请求
TrafficStats.java

AlarmManager, JobScheduler

###安全
HTTPS, 加密/解密

### 自定义控件
包括绘制流程
onMeasure() MeasureSpec
onLayout()
onDraw()
ViewRootImpl 
事件传递机制
View onTouchEvent, dispatchTouchEvent()
ViewGroup onInterceptTouchEvent()

### 四大引用
强(Strong)，软(Soft)废弃，弱(Weak)，虚(Phantom)
默认强引用,可能导致内存泄露
弱引用，只要GC就进行回收
虚引用，用于跟踪对象被垃圾回收的状态

### 间补动画和属性动画
ValueAnimator

### ``dalvik`` vs ``art``
art: ahead of time, improve gc

dalvik jit compile to native code, need more latency and memory.  
art (ahead of time) like jit, but run once at install time

### JNI
UnsatisfiedLinkError

### HashMap源码
默认大小4，载入因子0.75,时间和空间的
大小必须为2^n

## 深度

### OkHttp原理
优势:
  * 支持http2/spdy
  * socket自动选择最好线路，支持自动重连
  * socket连接池，减少握手次数
  * 队列线程池
  * 基于Headers的缓存策略

* 缓存策略(Header)
* 复用连接池
* 插件化适配
* 责任链

### Retrofit原理
动态代理，通过接口生成接口类, Proxy.newProxyInstance()。通过反射获取生成类的注解信息.AOP。

### ButterKnife原理
利用反射和APT

### JVM内存模型，性能调优
堆，方法区 所有线程共享
本地方法栈，虚拟机栈，程序计数器 线程独享

### 垃圾收集算法
gc算法
标记清除算法
标记整理算法
复制算法
分代收集算法

新生代和老年代(perm)
新生代包括eden和Survivor，比例在8:1:1
新生代采用复制算法,老年代采用标记整理算法


### Http/Https
* ssl加密传输，http明文传输
* 端口443, 80
* 需要证书
### 加密算法，对称和非对称区别
SHA,RSA,AES,DES
对称: 使用相同秘钥，需要在网络传输，安全性不高
非对称: 使用一对秘钥，公钥和私钥，私钥不在网络传输
### TCP/IP
三次握手，四次分手, TCP攻击

### 内存优化
内存及时释放。监控内存状态。

### code review
思路，结构
### 项目问题

### 学习能力

### 日志收集/处理

### 热修复技术
multiple dex

### 设计模式
单一责任原则
里氏替换原则
依赖倒置原则
接口隔离原则
迪米特原则(对象间耦合少)
开闭原则

### APK瘦身
Tinker

### 视频编解码
MediaCodec ffmpeg

### Framework源码
View绘制

### NDK/JNI
android.mk:包括需要编译模块
LOCAL_PATH: = $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE: = xxx
LOCAL_SRC_FILES: = xxx
application.mk: 编译的平台(ARMEABI), 工具链(GCC4.8), 标准库

Android Studio2.2.之后能够使用CMake(clang)
### Binder
Binder(C/S)
Activity
ActivityManagerService
ActivityManagerNative
ServiceManager

###Java内存模型
分为主内存和工作内存
主内存所有线程共享，工作内存各自线程独享
保证数据准确性，规定8种操作必须是原子性
volatile保证数据可见性。保证数据不被重排序
先行发生原则

