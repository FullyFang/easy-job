#深入理解HashTable
 ###Hash
 Hash，一般翻译做“散列”，也有直接音译为“哈希”的，就是把任意长度的输入（又叫做预映射， pre-image），通过散列算法，变换成固定长度的输出，
 该输出就是散列值。这种转换是一种压缩映射，也就是，散列值的空间通常远小于输入的空间，不同的输入可能会散列成相同的输出，
 而不可能从散列值来唯一的确定输入值。简单的说就是一种将任意长度的消息压缩到某一固定长度的消息摘要的函数。
 
 哈希表的存储过程如下：
 
 1、根据key计算出它的哈希表值h
 
 2、假设箱子个数为n，那么键值对应该放在第(h % n)个箱子中。
 
 3、如果该箱子已经有了键值对，就使用链表法解决冲突。
 (还有线性探测法、平方探测法、双平方探测法)
 
 哈希表还有一个重要的属性: 负载因子(load factor)，它用来衡量哈希表的空/满程度，一定程度上也可以体现查询的效率，
 计算公式为:
 
 loadFactor = keyValueNum / n;
 
 负载因子越大，意味着哈希表越满，越容易导致冲突，性能也就越低。
 因此，一般来说，当负载因子大于某个常数(可能是 1，或者 0.75 等)时，哈希表将自动扩容。
 
 哈希表在自动扩容时，一般会创建两倍于原来个数的箱子，因此即使 key 的哈希值不变，对箱子个数取余的结果也会发生改变，
 因此所有键值对的存放位置都有可能发生改变，这个过程也称为重哈希(rehash)。
### Java 8中的哈希表
HashMap 是基于 HashTable(synchronized) 的一种数据结构，在普通哈希表的基础上，
它支持多线程操作(非synchronized的)以及空的 key 和 value。
Java 5提供了ConcurrentHashMap，是hashTable的替代品
```java
//HashMap 中定义的几个常量
static final int DEFAULT_INITIAL_CAPACITY = 1 << 4; // aka 16  
static final int MAXIMUM_CAPACITY = 1 << 30;  
static final float DEFAULT_LOAD_FACTOR = 0.75f;  
static final int TREEIFY_THRESHOLD = 8;  
static final int UNTREEIFY_THRESHOLD = 6;  
static final int MIN_TREEIFY_CAPACITY = 64;  
```
TREEIFY_THRESHOLD: 上文说过，如果哈希函数不合理，即使扩容也无法减少箱子中链表的长度，
因此 Java 的处理方案是当链表太长时，转换成红黑树。这个值表示当某个箱子中，链表长度大于 8 时，有可能会转化成树。

