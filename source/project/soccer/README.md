# Soccer

### 难点
 * ``Sensor``分配，同步
   * 将``sensorId``分配到球员身上，只要扫到就算连接，比赛结束的时候，通过比赛开始时间，结束时间，球员在场时间，就能算出各个sensor的有效数据，多生产者，单消费者模型
 * ``Tracking``时间同步，由于``game``包括``tracking``和``capture``，因此两个界面的数据需要同步保持一致，``TrackingHelper``用于记录``duration``，单个``msg``来更新``duration``，现场恢复，记录最后动作的时间(包括暂停和恢复)
 * ``后台网络同步``，集锦制作，后台调度系统，``IntentService``断线重连，启动``app``重连，网络变化重连，优化，单任务，同步，吞吐量差，改用线程池，根据网络条件自动调整线程池大小
 * 数据封装，数据的差异性，``team report``, ``player report``
 * 视频播放缓存，异步下载，视频与播放不同步，内置服务器，在线播放直接缓存到内置服务器
 * 既是难点又是痛点，ui更新速度慢，使用``ReactNative``快速处理界面
 * 热修复，稳定性，覆盖率(Tinker, qfix, multipleDex)

### 优化

* 启动速度： 
   1. ``Application``, 启动速度降低%80(nexus5 5->1)
      * 由于将网络的初始化放在这里，通过``Debug.startMethodTrace()``检测到网络初始化，耗时超过80%
      * ``MixPanel``读取``apk``渠道。耗时近20%
      * 各种SDK的初始化(``LeanCloud``的初始化要在主线程),``facebook``
    2. ``SpalshActivity``