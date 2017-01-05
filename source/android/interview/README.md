# Interview

### ``Android``性能优化

 * 如何对``Android``应用进行性能分析
   * ``TraceView``, ``Sysdump``， ``Heap``, ``allocation tracker``, 
     * ``TraceView``: 分析页面渲染时间, ``startMethodTracking, stopMethodTracking``
     * ``Heap``: 检查内存泄露，观察``data object 的 TotalSize``
     * ``allocation tracker``:内存分配跟踪工具
 * ``ANR``
   * 目录``/data/data/anr/traces.txt``

 * 线程通讯
   * 共享内存
   * 文件，数据库
   * ``Handler``
 * 进程通讯
   * ``android``
     * ``Socket``
     * ``Binder``
     * ``Messenger``
     * ``Intents``
     * ``BroadcastReceivers``
   
   * ``Linux``
     * ``pipe``(管道)
     * ``semophore``(信号量)
     * ``messagequeue``(消息队列)
     * ``signal``(信号)
     * ``shared memory``(共享内存)

 * ``Dalvik``进程，``Linux``进程
   * ``Dalvik``虚拟机运行于``Linux``操作系统之上，在``Linux``中，只要两个进程共享一个地址空间，那么就可以认为它们是同一个进程的两个线程。``Linux``系统提供``fork``和``clone``调用，其中，前者用来创建进程，后者用来创建线程。

 * 描述一下``android``的系统架构
   * ``Linux kernel``
   * ``Runtime``
   * ``Framework``
   * ``Applicatoin``

 * ``Android``内存管理
   * 进程内存空间和``RAM``，操作系统会将程序运行中申请的内存映射到``RAM``，让进程能够使用物理内存
   * ``android``中的进程包括``native``进程和``java``进程
     * ``native``进程: 采用``C/C++``
     * ``java``进程: ``android``中运行与``dalvik``之上的进程。``dalvik``虚拟机宿主进程由``fork``系统调用创建.
     * 进程中的内存分布: 方法区， ``JAVA``栈， 本地方法栈， 堆， 程序计数器
     * ``dalvik``的``vm heapsize``有限制 (adb shell getprop | grep dalvik.vm.heapsize)，这样做的目的是为了让更多的程序常驻在内存中，以便快速启动
     * 进程优先级: 前台进程 > 可见进程 > 服务进程 > 后台进程 > 空进程
     * 提供进程优先级
       * 锁屏时，启动一个1像素的``Activity``，解锁时销毁，并且将其排除在最近启动列表
       * ``AndroidManifest.xml persistent = true``
       * 重载``back``，让``activity``在后台运行，不要``Destory``
       * 与``NotificationManager``交互，让进程变成前台进程
       * 发送/接收广播,别让自己变成空进程
       * 两个``Service``同时发出一个具有相同ID的``Notification``，然后将内部``Service``结束掉
       * 利用系统机制在``Service``死后自动拉活(``START_STICKY``)
       * 利用``Native``进程拉活
       * 5.0以上利用``JobScheduler``拉活
       * 利用账号同步拉活

   * 进程回收策略
     * 依靠``LowMemoryKiller``来完成，是一种根据``OOM_ADJ``阈值级别触发相应力度的内存回收机制[参考](https://segmentfault.com/a/1190000006251859)，``OOM_ADJ``越大越容易被回收
 * ``Android``运行时权限和文件系统权限的区别
 * ``Framework``的工作方式及原理
   * 所有的框架都是基于反射和配置文件的

 * 屏幕适配
   * ``dp, sp``
   * ``xxhdpi``
   * ``values-xxx(sw SmallWith)``
   * ``ConstrainLayout(percent)``

 * 动画
   * 补间动画(对``View``的属性不会造成影响)
   * 属性动画(对对象进行操作，会修改对象的属性)
   * 动画原理(待分析)
 
 * ``ContentObserver``
   * 观察者模式

 * 网络,``http/https``, ``tcp/ip``, ``tcp/udp``
   * ``TCP/IP``是个协议组，分为四层: 网络接口层，网络层，传输层，应用层
   * ``IP``处在网络层
   * 传输层中有``TCP, UDP``协议
     * **``TCP``**:

     1.  三次握手，第一次向``server``发送``SYN``包，等待``server``确认；第二次服务器收到``SYN``后，确认客户端的``SYN``，向客户端发送确认包``ACK`` SYN + ACK，服务器进入SYN_RECV状态；第三次，客户端收到``SYN+ACK``之后，向客户端发送``ACK``，发送完毕之后，建立连接。
     2. 面向连接，三次握手保持连接
     3. 面向字节流
     4. 头部开销20字节
       
     * **``UDP``** 
     
     1. 面向非连接，用户可能需要校验数据,
     2. 面向报文，没有阻塞控制
     3. 头部开销8字节
     
   * ``SOCKET``属于``API``,是对``TCP/IP UDP``的封装
     * 服务器监听
     * 客户端请求
     * 连接确认
   * ``HTTP``属于应用层协议
     * 1.0，一次请求，一次连接，自动释放， 2.0一次连接处理多个请求，并且多个请求可以同时进行
     * 短连接
     * ``GET``: 从指定的``URL``获取内容,大小有限制(4k)，不安全，只允许``ASCII``字符
     * ``POST``: 提交``body``中的数据给服务器，也允许二进制数据
     * ``HEAD``: 从指定的``url``上获取``header``内容
     * ``PUT``: 将``body``上传至服务器
     * ``DELETE``: 删除资源
   * ``HTTP``是轿车，提供了封装或者显示数据的具体形式，``Socket``是发动机，提供了网络通讯的能力
   * ``HTTPS``，在``HTTP``下加入了``SSL``层,``HTTPS``需要证书,``HTTP``是明文传输，``HTTPS``采用``SSL``加密
   * ``HTTP``端口是80, ``HTTPS``是443
 * 推送
   * 心跳， ``AlarmManager``来定时发送心跳包

 * 自定义控件
 
 * 设计模式

 * 算法

 * ``Java``内存管理

 * ``Java``虚拟机和``Dalvik``虚拟机
   * ``Java``虚拟机运行的是``Java``字节码, ``Dalvik``运行的是``dex``

 * ``插件化开发``
 * ``WindowManager``
 * ``Java虚拟机``和``Dalvik``虚拟机
   * ``Java``虚拟机基于栈，``Dalvik``基于寄存器
   * ``Java``虚拟机基于寄存器，``Dalvik``基于``dex``
   * ``Dalvik``虚拟机已被修改为只使用32位的索引，以简化解释器。``Dalvik``虚拟机的堆和栈的参数可以通过``-Xms``和``-Xmx``更改
 
 * 优化
   * ``TraceView``的使用，``Android Device Monitor``->``Start method Profiling``,也可以直接使用``Debug.startMethodProfiling``。主要关注: ``IncI``包括子函数耗时,``Excl``不包括子函数耗时。重点关注项: ``Calls+RecurCalls/Total``，``cpu time call``。
   * ``dmtracedump``函数调用关系图
   * ``MemoryAnalyzer ``内存分析
   * ``Systrace``(黄点和红点表示该帧渲染时间超过16.6ms)效果会好于``TraceView``)
     * ``framework: Trace.traceBegin(), Trace.traceEnd(), app: Trace.beginSection(), Trace.endSection()``

### TCL(工具开发，清理大师)
 * ``PICASSO``源码分析:特点
 * 第三方库再封装便宜替换
 * 架构特点:如果避免内存泄露
 * ``Binder``分析，进程间通讯
 * 优化
 * 进程保活
 
### Grab(东南亚打车)(二面)
#### 一面
 * 项目难点，挑战。``ReactNative``，性能，有没有想过使用``V8``来统一编译
 * ``AsyncTask``的优点，弱点，有点线程自动切换，弱点多线程拓展能力弱，生命周期不好控制
 * 网络协议设计，优化，思路。定指标，网络发包测试。在不同的网络环境下测试。场景复现测试
 * 编写``RingBuffer``

#### 二面
 * 项目难点，项目介绍
 * ``ANR``，一个DEMO，在``onClick``中做大量超时操作(只点击一次，等待)，会出现``ANR``吗？会(其实不会)
 * ``Handler``原理,主线程给主线程发消息，内存泄露
 * ``Activity Launch Mode``(很糟糕)
 * ``Service``相关(startService, bindService)，生命周期
 * ``x^n``

#### 三面
 * 白板编程，编写``Stack``
 * 怎么测试,``Junit4``单元测试,``Android``中哪些东西可以进行单元测试。回答``api``测试，业务逻辑测试。测试用例集成到``CI Genkins``上
 * ``ContentProvider``与``Sqlite``配合使用,使用``ContentObserver``来监听数据库变化。
 * ``MVP``中的``P``不应该有``V``相关的东西，那么如何解耦。使用事件总线(``EventBus``, ``RxBus``)或者``Dagger2``依赖注入
 * ``RecyclerView``中每个``item``需要发送请求，改如何写。直接在``adapter``中写不好。使用``Event``的方式通知``Domain Layer``发送网络请求，然后刷新。也可以使用``ContentObserver``来监听数据库变化
 * ``Realm``和``RxJava``的配合使用