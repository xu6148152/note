# Experience

* **Library中R文件的ID不是final**
  * 因此在``switch case``中无法使用``id``

* **同一个程序的多进程操作SharePreference不安全**
  * 可以使用``ContentProvider``来替代``SharePreference``,[tray](https://github.com/grandcentrix/tray)
  
* **TypeFace初始化字体很慢**，应该在非主线程来进行初始化  

* **Bundle传递的数据必须小于1M**
* **数据库升级，需要手动将旧数据拷到新数据库**
* **程序新安装未启动过** ，无法接收到静态广播。可以通过其他应用发送指定的FLAG, FLAG_INCLUDESTOPPEDPACKAGES, FLAGEXCLUDESTOPPEDPACKAGES
* **AS中同一个工程MODULE太多build很慢, 可以把不常改动的module打包成aar**
* ``evalation``的使用必须设置``background``，要在设置``evalation``的``view``上显示必须设置``translationZ``大于``evalation``
*  > Failed to notify project evaluation listener.
   > Configuration with name 'default' not found.
   
   项目中可能存在循环引用，需要排查