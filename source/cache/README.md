# Cache

#### 概念

缓存就是数据池，这些数据是从数据库里的真实数据复制出来的，并且为了能被取回，被标上了标签(键ID)。

####缓存算法

* **Least Frequently Used(LFU)**
  * 计算每个缓存对象的使用频率，把不常用的删除
* **Least Recently Used(LRU)**
  * 把最近最少使用的删除
* **Least Recently Used(LRU2)**
  * 把最近最少使用两次的缓存对象删除
* **Two Queues(2Q)**
  * 把访问的数据放到``LRU``中，如果这个对象被再一次访问，把它转移到第二个更大的``LRU``缓存。保持第一个缓存池是第二个的1/3
* **Adaptive Replacement Cache(ARC)**
  * 由两个``LRU``组成。第一个存储最近只被使用过一次的缓存对象，第二个存储最近被使用过两次的缓存对象
  * 性能最好的缓存算法之一
* **Most Recently Used(MRU)**
  * 移除被使用最多的对象。
* **First in First out(FIFO)**
  * 先进先出算法
* **Second Chance**
  * 改进``FIFO``，检查即将要被删除对象有没有被使用过的标志(1bit)，没被使用过，直接删除，被使用过，清除标记，重新加入队列
* **CLock**
  * 更好的``FIFO``，使用环形队列，头指针指向列表中最老的缓存对象,当有新缓存时，会判断指针指向的对象的标志位以决定直接替换该对象,或者直接进入队列
* **Random Cache**
  * 随机缓存，性能比``FIFO``好，比``LRU``差

**缓存算法主要考虑几点**:

 * 成本
 * 容量
 * 时间

**缓存元素**

 ```java
 public class CacheElement {
 	private Object objectValue;
 	private int index;
 	private Object objectKey;
 	private int hitCount;
 ```

**缓存算法公用代码**

```java
public final synchronized void addElement(Object key, Object value) {
	int index;
	Object obj;
	obj = table.get(key);
	if(obj != null) {
		CacheElement element;
		element = (CacheElement)obj;
		element.setObjectValue(value);
		element.setObjectKey(key);
		return;
	}
}
```  