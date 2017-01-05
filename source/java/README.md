# Java

 * Enumeration and an Iterator
  * remove()
  
 * fast-fail: common collection
  * ConcurrentModificationException
 
 * fail-safe: CopyOnWriteArrayList, ConcurrentHashMap
  * Collection Copy
  
 * Pergem: Permanent Generation  OutOfMemoryError

 * 垃圾回收
  * 区分三个分区，年轻，年老，永久，根据对象存活的时间移动到对应的区域

 * ``Transient``: 
   * ``Java7``加入的不允许序列化的关键字,Variables may be marked transient to indicate that they are not part of the persistent state of an object.
 
 * 集合:
 	* ``HashMap``:
   		* 底层数据结构: ``Android``是``HashMapEntry``表, ``Java``是``Node``表
    * ``TreeMap``:
       * 红黑树