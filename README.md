# java-base
集合分为两大块：java.util包下的非线程安全集合和java.util.concurrent下的线程安全集合。

java中线程安全和非线程安全的集合

ArrayList与LinkedList的实现和区别

1、Vector：
Vector与ArrayList一样，也是通过数组实现的，不同的是它支持线程的同步，即某一时刻只有一个线程能够写Vector，避免多线程同时写而引起的不一致性，但实现同步需要很高的花费，因此，访问它比访问ArrayList慢。
2、ArrayList：
a. 当操作是在一列数据的后面添加数据而不是在前面或者中间，并需要随机地访问其中的元素时，使用ArrayList性能比较好。
b. ArrayList是最常用的List实现类，内部是通过数组实现的，它允许对元素进行快速随机访问。数组的缺点是每个元素之间不能有间隔，当数组大小不满足时需要增加存储能力，就要讲已经有数组的数据复制到新的存储空间中。当从ArrayList的中间位置插入或者删除元素时，需要对数组进行复制、移动、代价比较高。因此，它适合随机查找和遍历，不适合插入和删除。
3、LinkedList：
a. 当对一列数据的前面或者中间执行添加或者删除操作时，并且按照顺序访问其中的元素时，要使用LinkedList。
b. LinkedList是用链表结构存储数据的，很适合数据的动态插入和删除，随机访问和遍历速度比较慢。另外，他还提供了List接口中没有定义的方法，专门用于操作表头和表尾元素，可以当作堆栈、队列和双向队列使用。



HashMap：了解其数据结构、hash冲突如何解决（链表和红黑树）、扩容时机、扩容时避免rehash的优化

1、HashMap底层就是一个数组结构，数组中的每一项又是一个链表。数组+链表结构，新建一个HashMap的时候，就会初始化一个数组。Entry就是数组中的元素，每个Entry其实就是一个key-value的键值对，它持有一个指向下一个元素的引用，这就构成了链表，HashMap底层将key-value当成一个整体来处理，这个整体就是一个Entry对象。HashMap底层采用一个Entry【】数组来保存所有的key-value键值对，当需要存储一个Entry对象时，会根据hash算法来决定在其数组中的位置，在根据equals方法决定其在该数组位置上的链表中的存储位置；当需要取出一个Entry对象时，也会根据hash算法找到其在数组中的存储位置， 在根据equals方法从该位置上的链表中取出Entry

2、解决hash冲突方法：

开放定址法(查询产生冲突的地址的下一个地址是否被占用，直到寻找到空的地址)，

再散列法（有多个不同的Hash函数，当发生冲突时，使用第二个，第三个），

链地址法（每个哈希表节点都有一个next指针，被分配到同一个索引上的多个结点用单向链表连接起来）。

hashmap采用的就是链地址法，jdk1.7中，当冲突时，在冲突的地址上生成一个链表，将冲突的元素的key，通过equals进行比较，相同即覆盖，不同则添加到链表上，jdk1.7之前是插入链表头部的。此时如果链表过长，效率就会大大降低，查找和添加操作的时间复杂度都为O(n)；但是在jdk1.8中如果链表长度大于8，链表就会转化为红黑树，在jdk1.8中是插入链表的尾部的，时间复杂度也降为了O(logn)，性能得到了很大的优化。

3、HashMap的初始容量16，加载因子为0.75，扩容增量是原容量的1倍。如果HashMap的容量为16，一次扩容后容量为32。HashMap扩容是指元素个数（包括数组和链表+红黑树中）超过了16*0.75=12（容量×加载因子）之后开始扩容。

  扩容必须满足两个条件

存放新值的时候当前已有元素必须大于阈值；
存放新值的时候当前存放数据发生hash碰撞（当前key计算的hash值计算出的数组索引位置已经存在值）
加载因子越大,填满的元素越多,空间利用率越高，但冲突的机会加大了。反之,加载因子越小,填满的元素越少,冲突的机会减小,但空间浪费多了（因为需要经常扩容）。所以这是一个时间和空间的均衡
rehash优化：在JDK1.7的时候，是将数组扩容为两倍，然后将HashMap中所有的key重新进行hash寻址然后再放入到新的位置

JDK1.8 优化成直接把链表拆成高位和低位两条，通过位运算来决定放在原索引处或者原索引加原数组长度的偏移量处：

HashMap 的扩容都是扩大为原来大小的两倍，从二进制上看就是给这串数字加个 0，比如 16 -> 32 = 10000 -> 100000，那么他的 n - 1 就是 15 -> 32 = 1111 -> 11111。也就是多了一位，所以扩容后的下标可以从原有的下标推算出来。差异就在于上图我标红的地方，如果标红处是 0，那么扩容后再求余结果不变，如果标红处是 1，那么扩容后再求余就为原索引 + 原偏移量。如何判断标红处是 0 还是 1，就是把 e.hash & oldCap。

红黑树的拆分和链表的逻辑基本一致，不同的地方在于，重新映射后，会将红黑树拆分成两条链表，根据链表的长度，判断需不需要把链表重新进行树化。

LinkedHashMap：了解基本原理、哪两种有序、如何用它实现LRU

LinkedHashMap 继承自 HashMap，所以它的底层仍然是基于拉链式散列结构。该结构由数组和链表+红黑树 在此基础上LinkedHashMap 增加了一条双向链表，保持遍历顺序和插入顺序一致的问题。

LinkedHashMap是有序的，有两种顺序：插入顺序和访问顺序。默认为插入顺序，如果为访问顺序，那么put和get已存在的节点时，会将该节点移动到双向链表的尾部
插入顺序和访问顺序的转换是定义accessOrder参数的值，默认为false插入顺序，为true则表示为访问顺序
LinkedHashMap与HashMap的存取数据操作基本是一致的，只是增加了双向链表保证数据的有序性
LinkedHashMap与HashMap都是线程不安全的。
LinkedHashMap的removeEldestEntry方法默认返回false，要实现LRU很重要的一点就是集合满时要将最久未访问的元素删除，在LinkedHashMap中这个元素就是头指针指向的元素。实现LRU可以直接实现继承LinkedHashMap并重写removeEldestEntry方法来设置缓存大小。
TreeMap：了解数据结构、了解其key对象为什么必须要实现Compare接口、如何用它实现一致性哈希？

TreeMap实现了SotredMap接口，它是有序的集合。而且是一个红黑树结构，每个key-value都作为一个红黑树的节点。如果在调用TreeMap的构造函数时没有指定比较器，则根据key执行自然排序。

1、存入TreeMap的键值对的key是要能自然排序的（实现了Comparable接口），否则就要自定义一个比较器Comparator作为参数传入构造函数。

2、TreeMap是以红黑树将数据组织在一起，在添加或者删除节点的时候有可能将红黑树的结构破坏了，所以需要判断是否对红黑树进行修复。

3、由于底层是红黑树结构，所以TreeMap的基本操作 containsKey、get、put 和 remove 的时间复杂度是 log(n) 。 

4、由于TreeMap实现了NavigableMap，所以TreeMap有一系列的导航方法。

hashmap如何解决hash冲突，为什么hashmap中的链表需要转成红黑树？

红黑树插入为O(lgn),查询为O(lgn)，链表插入为O(1)，查询为O(n)。个数少时，插入删除成本高，用链表；个数多时，查询成本高，用红黑树。

hashmap什么时候会触发扩容？

当put时发现table未初始化时，进行初始化扩容
当put加入节点后，发现size（键值对数量）>threshold时，进行扩容
jdk1.8之前并发操作hashmap时为什么会有死循环的问题？

在jdk1.7中，由于扩容时使用头插法，两个线程同时执行扩容时可能会形成环状列表，导致hashMap调用get方法时死循环，在jdk1.8中改为尾插法，可以避免这种问题。但是hashmap依然是非线程安全的，多线程下建议使用ConcurrentHashMap。

hashMap扩容时每个entry需要再计算一次hash吗？

jdk1.7的hashmap扩容时需要重新计算

jdk1.8的源码中没有重新计算hash，如果hash对应该桶有链表，使用(e.hash & oldCap) == 0来判断hash对应新增的(n-1)的二进制位上的值是1还是0，判断索引不变或者变成“原索引+oldCap”。

hashmap的数组长度为什么要保证是2的幂？

jdk1.7中有一个indexForxxx()方法用来计算键值对在数组中的下标。jdk1.8中没有这个方法，但也使用了表达式(n-1) & hash(其中n为数组的长度)代替这个方法。取余(%)操作中如果除数是2的幂次则等价于与其除数减一的与(&)操作（也就是说hash%length==hash&(length-1)的前提是 length 是2的 n 次方）。并且 采用二进制位操作 &，相对于%能够提高运算效率，这就解释了 HashMap 的长度为什么是2的幂次方。

如何用LinkedHashMap实现LRU？

LinkedHashMap底层就是用的HashMap加双链表实现的，而且本身已经实现了按照访问顺序的存储。此外，LinkedHashMap中本身就实现了一个方法removeEldestEntry用于判断是否需要移除最不常读取的数，方法默认是直接返回false，不会移除元素，所以需要【重写该方法】，即当缓存满后就移除最不常用的数。

@Override protected boolean removeEldestEntry(Entry eldest) {

if(cacheSize + 1 == map.size())

{ return true;

}else{

return false;

}}

如何用TreeMap实现一致性hash？

待解决

Collections.synchronized 了解其实现原理

synchronized是JVM实现的一种互斥同步访问方式，底层是基于每个对象的监视器(monitor)来实现的。被synchronized修饰的代码，在被编译器编译后在被修饰的代码前后加上了一组字节指令。
在代码开始加入了monitorenter，在代码后面加入了monitorexit，这两个字节码指令配合完成了synchronized关键字修饰代码的互斥访问。
在虚拟机执行到monitorenter指令的时候，会请求获取对象的monitor锁，基于monitor锁又衍生出一个锁计数器的概念。
当执行monitorenter时，若对象未被锁定时，或者当前线程已经拥有了此对象的monitor锁，则锁计数器+1，该线程获取该对象锁。
当执行monitorexit时，锁计数器-1，当计数器为0时，此对象锁就被释放了。那么其他阻塞的线程则可以请求获取该monitor锁。

CopyOnWriteArrayList 了解写时复制机制、了解其适用场景、思考为什么没有ConcurrentArrayList

CopyOnWriteArrayList 是一个写时复制的容器，当我们往一个容器添加元素的时候，不直接往当前容器添加，而是先将当前容器进行 Copy，复制出一个新的容器，然后往新的容器里添加元素，添加完元素之后，再将原容器的引用指向新的容器。这样做的好处是我们可以对 CopyOnWriteArrayList 进行并发的读，而不需要加锁，因为当前容器不会添加任何元素。所以 CopyOnWriteArrayList 是一种读写分离的容器，适用于读多写少的场景，支持高并发读取。

线程安全：使用了 ReentrantLock 独占锁，保证同时只有一个线程对集合进行修改操作。

 CopyOnWrite 思想：写时复制。就是在写的时候，拷贝一份原对象，只操作拷贝的对象，操作完后再覆盖原对象，保证 volatile 语义。

CopyOnWriteArrayList 的缺点是什么？

占用内存问题。因为 CopyOnWrite 的写时复制机制，所以在进行写操作的时候，内存里会同时驻扎两个对象的内存，旧的对象和新写入的对象，这样如果数据很大可能造成频繁的 GC。
数据一致性问题。因为 CopyOnWrite 容器支持读写分离，所以只能保证数据的最终一致性，不能保证实时一致性。
为什么java.util.concurrent 包里没有并发的ArrayList实现？

原因：很难去开发一个通用并且没有并发瓶颈的线程安全的List

ConcurrentHashMap这样的类的真正价值（The real point / value of classes）并不是它们保证了线程安全。而在于它们在保证线程安全的同时不存在并发瓶颈。举个例子，ConcurrentHashMap采用了锁分段技术和弱一致性的Map迭代器去规避并发瓶颈。像“Array List”这样的数据结构，你不知道如何去规避并发的瓶颈。拿contains() 这样一个操作来说，当你进行搜索的时候如何避免锁住整个list？

CopyOnWriteArrayList它规避了只读操作（如get/contains）并发的瓶颈，但是它为了做到这点，在修改操作中做了很多工作和修改可见性规则。 此外，修改操作还会锁住整个List，因此这也是一个并发瓶颈。所以从理论上来说，CopyOnWriteArrayList并不算是一个通用的并发List。

ConcurrentHashMap了解实现原理、扩容时做的优化、与HashTable对比。

在 JDK1.7 中，ConcurrentHashMap 是由 Segment 数组结构和 HashEntry 数组结构组成。Segment 继承了 ReentrantLock，是一种可重入锁。HashEntry 则用于存储键值对数据。一个 ConcurrentHashMap 里包含一个 Segment 数组，一个 Segment 里包含一个 HashEntry 数组 ，每个 HashEntry 是一个链表结构的元素，因此 JDK1.7 的 ConcurrentHashMap 是一种数组+链表结构。当对 HashEntry 数组的数据进行修改时，必须首先获得与它对应的 Segment 锁，这样只要保证每个 Segment 是线程安全的，也就实现了全局的线程安全（分段锁）

在 JDK1.8 中，ConcurrentHashMap 选择了与 HashMap 相同的数组+链表+红黑树结构，在锁的实现上，采用 CAS 操作和 synchronized 锁实现更加低粒度的锁，将锁的级别控制在了更细粒度的 table 元素级别，也就是说只需要锁住这个链表的首节点，并不会影响其他的 table 元素的读写，大大提高了并发度。

扩容时机：

添加新元素后，元素个数达到扩容阈值触发扩容。

调用 putAll 方法，发现容量不足以容纳所有元素时候触发扩容。

某个槽内的链表长度达到 8，但是数组长度小于 64 时候触发扩容。

扩容机制：

首先每个线程承担不小于 16 个槽中的元素的扩容，然后从右向左划分 16 个槽给当前线程去迁移，每当开始迁移一个槽中的元素的时候，线程会锁住当前槽中列表的头元素，假设这时候正好有 get 请求过来会仍旧在旧的列表中访问，如果是插入、修改、删除、合并、compute 等操作时遇到 ForwardingNode，当前线程会加入扩容大军帮忙一起扩容，扩容结束后再做元素的更新操作。已完成节点复制的的node会进行标记，防止多线程下的混乱。

与HashTable对比

Hashtable通过使用synchronized修饰方法的方式来实现多线程同步，因此，Hashtable的同步会锁住整个数组。在高并发的情况下，性能会非常差，ConcurrentHashMap作为高吞吐量的线程安全HashMap实现，它采用了锁分离的技术允许多个修改操作并发进行。ConcurrentHashMap默认将hash表分为16个桶，诸如get、put、remove等常用操作只锁住当前需要用到的桶。这样，原来只能一个线程进入，现在却能同时有16个写线程执行，并发性能的提升是显而易见的。

BlockingQueue：了解LinkedBlockingQueue、ArrayBlockingQueue、DelayQueue、SynchronousQueue

1、ArrayBlockingQueue：内部基于数组实现的有界阻塞队列，按照先进先出（FIFO）的原则对元素进行排序，支持公平锁和非公平锁。由于对于生产者放入数据和消费者取出数据使用的是同一个锁，所以二者无法真正并行运行。同时由于数组中存放的直接是放入的数据，所以不会产生额外的对象实例。

2、LinkedBlockingQueue：内部基于链表实现的有界阻塞队列（默认Integer.MAX_VALUE），按照先进先出的顺序对元素存取，内部使用非公平锁。对于生产者和消费者使用的是两把不同的锁所以生产者和消费者可以同步进行。由于是使用的链表结构，所以会生成Node节点对象实例，销毁的时候需要额外处理。

3、DelayQueue：内部基于非线程安全的优先队列实现的无界阻塞队列，内部使用非公平锁。DelayQueue中的元素只有当其指定的延迟时间到了，才能够从队列中获取到该元素。使用场景：1.缓存系统的设计：可以用DelayQueue保存缓存元素的有效期，使用一个线程循环查询DelayQueue，一旦能从DelayQueue中获取元素时，表示缓存有效期到了。2.定时任务调度。使用DelayQueue保存当天将会执行的任务和执行时间，一旦从DelayQueue中获取到任务就开始执行，从比如TimerQueue就是使用DelayQueue实现的。

5、SynchronousQueue：内部是基于无缓冲并且无界的等待队列，但是由于该Queue本身的特性，在某次添加元素后必须等待其他线程取走后才能继续添加；可以认为SynchronousQueue是一个缓存值为1的阻塞队列；

ConcurrentHashMap是如何在保证并发安全的同时提高性能？

ConcurrentHashMap所使用的锁分段技术，首先将数据分成一段一段的存储，然后给每一段数据配一把锁，当一个线程占用锁访问其中一个段数据的时候，其他段的数据也能被其他线程访问。有些方法需要跨段，比如size()和containsValue()，它们可能需要锁定整个表而而不仅仅是某个段，这需要按顺序锁定所有段，操作完毕后，又按顺序释放所有段的锁

ConcurrentHashMap是如何让多线程同时参与扩容？

根据操作系统的 CPU 核数和集合 length 计算每个核一轮处理桶的个数，最小是16
修改 transferIndex 标志位，每个线程领取完任务就减去多少，比如初始大小是transferIndex = table.length = 64，每个线程领取的桶个数是16，第一个线程领取完任务后transferIndex = 48，也就是说第二个线程这时进来是从第 48 个桶开始处理，再减去16，依次类推，这就是多线程协作处理的原理
领取完任务之后就开始处理，如果桶为空就设置为 ForwardingNode ,如果不为空就加锁拷贝，只有这里用到了 synchronized 关键字来加锁，为了防止拷贝的过程有其他线程在put元素进来。拷贝完成之后也设置为 ForwardingNode节点。
如果某个线程分配的桶处理完了之后，再去申请，发现 transferIndex = 0，这个时候就说明所有的桶都领取完了，但是别的线程领取任务之后有没有处理完并不知道，该线程会将 sizeCtl 的值减1，然后判断是不是所有线程都退出了，如果还有线程在处理，就退出
直到最后一个线程处理完，发现 sizeCtl = rs<< RESIZE_STAMP_SHIFT 也就是标识符左移 16 位，才会将旧数组干掉，用新数组覆盖，并且会重新设置 sizeCtl 为新数组的扩容点。
LinkedBlockingQueue、DelayQueue是如何实现的？

CopyOnWriteArrayList是如何保证线程安全的？

synchronized：了解偏向锁、轻量级锁、重量级锁的概念以及升级机制、以及和ReentrantLock的区别

偏向锁，顾名思义，它会偏向于第一个访问锁的线程，如果在接下来的运行过程中，该锁没有被其他的线程访问，则持有偏向锁的线程将永远不需要触发同步（Monitor）。
 如果在运行过程中，遇到了其他线程抢占锁，则持有偏向锁的线程会被挂起，JVM会尝试消除它身上的偏向锁，将锁恢复到标准的轻量级锁。
轻量级锁,一般用于两个线程在交替使用锁的时候，由于没有同时抢锁，属于一种比较和谐的状态，就可以使用轻量级锁。
重量级锁：当轻量级锁膨胀到重量级锁之后，意味着线程只能被挂起阻塞来等待唤醒了。每一个对象中都有一个Monitor监视器，而Monitor依赖操作系统的 MutexLock(互斥锁)来实现的, 线程被阻塞后便进入内核（Linux）调度状态，这个会导致系统在用户态与内核态之间来回切换，严重影响锁的性能。

升级机制：偏向锁是默认开启的，线程1访问对象时，获取该对象的偏向锁；

如果线程2需要进入同步方法，线程1还持有这个对象，那么就会进入偏向锁→轻量级锁的过程，此时线程2进行cas替换失败，会修改对象头，升级为轻量级锁，同时开启自旋，重复尝试替换；

轻量级锁替换失败到达一定次数（默认为10）后，轻量级锁升级为重量级锁。如果线程2自旋期间，有线程3也需要访问同步方法，则立刻由轻量级锁膨胀为重量级；

synchronized和ReentrantLock区别：

① 底层实现上来说：synchronized 是JVM层面的锁，是Java关键字，通过monitor对象来完成（monitorenter与monitorexit），对象只有在同步块或同步方法中才能调用wait/notify方法，ReentrantLock 是从jdk1.5以来（java.util.concurrent.locks.Lock）提供的API层面的锁。

② 是否可手动释放：synchronized 不需要用户去手动释放锁，synchronized 代码执行完后系统会自动让线程释放对锁的占用； ReentrantLock则需要用户去手动释放锁，如果没有手动释放锁，就可能导致死锁现象。一般通过lock()和unlock()方法配合try/finally语句块来完成，使用释放更加灵活。

③ 是否可中断：synchronized是不可中断类型的锁，除非加锁的代码中出现异常或正常执行完成； ReentrantLock则可以中断，可通过trylock(long timeout,TimeUnit unit)设置超时方法或者将lockInterruptibly()放到代码块中，调用interrupt方法进行中断。

④ 是否公平锁：synchronized为非公平锁， ReentrantLock则即可以选公平锁也可以选非公平锁，通过构造方法new ReentrantLock时传入boolean值进行选择，为空默认false非公平锁，true为公平锁。

⑤ 锁是否可绑定条件Condition：synchronized不能绑定； ReentrantLock通过绑定Condition结合await()/singal()方法实现线程的精确唤醒，而不是像synchronized通过Object类的wait()/notify()/notifyAll()方法要么随机唤醒一个线程要么唤醒全部线程。

⑥ 锁的对象：synchronzied锁的是对象，锁是保存在对象头里面的，根据对象头数据来标识是否有线程获得锁/争抢锁；ReentrantLock锁的是线程，根据进入的线程和int类型的state标识锁的获得/争抢。

CAS：了解AtomicInteger实现原理、CAS适用场景、如何实现乐观锁

AtomicInteger是一个提供原子操作的Integer类，通过线程安全的方式操作加减。因此十分适合高并发情况下的使用。

AtomicInteger在操作get的时候，先获取到当前的 value 属性值，但是内部有一个死循环，不断去做compareAndSet操作，通过对比预期值和内存值是否相同来判断是否返回，直到成功为止。因此是线程安全。

使用CAS在线程冲突严重的情况下，会大幅降低程序性能，CAS只适用于线程冲突较少的情况下使用；synchronized在线程冲突较少的情况下，可以获得和CAS类似的性能，而线程冲突严重的情况下，性能远高于CAS

实现乐观锁：

1版本号机制：一般是在数据表中加上版本号字段 version，表示数据被修改的次数。当数据被修改时，这个字段值会加1。

2CAS 算法：当且仅当预期值A和内存值V相同时，将内存值V修改为新值B，否则不会执行任何操作（比较和替换是一个 native 原子操作）。一般情况下，这是一个自旋操作，即不断的重试。

AQS队列同步器：了解AQS内部实现、及依靠AQS的同步类比如ReentrantLock、Semaphore、CountDownLatch、CyclicBarrier等的实现

从使用层面来说，AQS功能分为两种：独占和共享

独占锁，每次只能一个线程持有锁，比如ReentrantLock就是独占锁
共享锁，允许多个线程持有锁，并发访问共享资源，比如ReentrantReadWriteLock
共享锁和独占锁的释放有一定区别，前面部分是一致的，先判断头结点是不是signal状态，如果是则唤醒头节点的下一个节点，并将该节点设置为头结点。而共享锁不一样，某个节点被设置为head之后，如果它的后继节点是shared状态，那么会尝试使用doReleaseShared方法尝试唤醒节点，实现共享状态的传播。

AQS是依赖内部的同步队列实现，也就是FIFO双向队列，如果当前线程竞争锁失败，那么AQS会把当前线程以及等待状态封装成一个Node节点加入到同步队列中，同时阻塞该线程，当同步状态释放时，会把首节点唤醒，使其再次尝试获取同步状态。

AQS的阻塞和唤醒：调用LockSupport工具类的park()方法来阻塞；执行结束后采用tail回溯办法找第一个可用的线程。最后调用LockSupport的unpark(Thread thread)方法唤醒该线程。

RentrantLock的实现：

初始时，线程A调用RentrantLock的lock方法尝试加锁，加锁的过程是用CAS将state由0变为1，此时线程A获取锁成功，将加锁线程设置为自己

可重入锁：若线程A再次获取到了锁，判断当前加锁线程是否是自己，是的话再次获取锁成功，state加1

互斥锁：线程B想要获取锁，先判断state是否为0：state = 0，尝试获取锁；state != 0，看持有锁的线程是否是自己；不是则进入到等待队列。线程B等待线程A释放锁之后，尝试重新获取

释放锁：state变量减一，为0时彻底释放锁，不再持有资源，直至加锁线程为null。接下来从等待队列的对头唤醒线程B，重新尝试加锁

RentrantLock实现公平和非公平锁：Sync类直接继承自AQS，它的子类NonfairSync、FairSync分别实现了获取锁的非公平与公平策略；非公平直接判断status=0来获取锁，公平策略多一个判断当前队列的首节点是否当前节点。

Semaphore的使用（通常用来限制线程的数量）：

1.调用acquire来获取令牌（cas操作status-1），如果获取到，则继续运行，运行完成之后，调用release方法（cas操作status+1）释放令牌供后面的线程使用。

2.如果获取不到，则等待（AQS队列等待），直到有令牌空闲出来，其才会被唤醒然后获取令牌之后继续运行

信号量的核心功能就是用来对资源做一定的限制，防止出现崩塌现象。最适用的应用场景那就是限流，通过限流来保护对应的资源。

CountDownLatch方法（限制数量为count的线程执行，其余的等待）：

public CountDownLatch(int count)构造函数，count代表计数器个数（内部是共享锁，本质就是上了几次锁）

public void countDown()每countDown一次，计数器就减一，就是释放了一次共享锁，直到为0全部结束二

public void await()在AQS队列里一直等待，直到countDown减到0，才会继续往下执行

CyclicBarrier是一种同步辅助工具（等待所有线程一起执行），字面意思就是循环栅栏，它允许一组线程在一个共同的屏障点彼此等待，所有线程到达屏障点后再全部同时执行。固定数量的线程在程序中必须彼此等待的时候，CyclicBarrier非常有用。

在CyclicBarrier的内部定义了一个ReentrantLock的对象，然后再利用这个ReentrantLock对象生成一个Condition的对象。每当一个线程调用CyclicBarrier的await方法时，首先把剩余屏障的线程数减1，然后判断剩余屏障数是否为0：如果不是，利用Condition的await方法阻塞当前线程；如果是，首先利用Condition的signalAll方法唤醒所有线程，最后重新生成Generation对象以实现屏障的循环使用。

CountDownLatch和CyclicBarrier使用场景和区别：CountdownLatch适用于所有线程通过某一点后通知方法,而CyclicBarrier则适合让所有线程在同一点同时执行。

了解ThreadLocal使用场景和内部实现

使用场景：实现线程间的数据隔离：当我们只想在本身的线程内使用的变量，可以用 ThreadLocal 来实现，并且这些变量是和线程的生命周期密切相关的，线程结束，变量也就销毁了。（比如多线程下的数据库连接）

内部实现：每个Thread 维护一个 ThreadLocalMap 映射表，这个映射表的 key 是 ThreadLocal实例本身，value 是真正需要存储的 Object。

使用ThreadLocal时要注意什么？比如说内存泄漏?

ThreadLocal自身并不储存值，而是作为一个key来让线程从ThreadLocal获取value。Entry是中的key是弱引用，所以jvm在垃圾回收时如果外部没有强引用来引用它，ThreadLocal必然会被回收。但是，作为ThreadLocalMap的key，ThreadLocal被回收后，ThreadLocalMap就会存在null，但value不为null的Entry。若当前线程一直不结束，可能是线程结束后不被销毁，或者分配使用了又不再调用get/set方法（调用方法会清除key=null的value），就可能引发内存泄漏。

避免：在使用完ThreadLocal时，及时调用它的的remove方法清除数据。

ThreadPoolExecutor：了解线程池的工作原理以及几个重要参数的设置

线程池工作：事物提交到线程池，当前线程小于核心数则直接创建线程执行；大于核心数则加入队列等待；队列已满且线程数小于最大线程数，则创建临时线程执行；大于最大线程数则拒绝执行；线程处于空闲的时间超过keepAlive且线程数大于核心，则回收。

常用线程创建：newSingleThreadExecutor单线程的线程池、newFixedThreadExecutor创建固定大小的线程池、newCacheThreadExecutor可缓存的线程池、newScheduleThreadExecutor大小无限的线程池

重要参数：

corePoolSize：核心线程数量
maximumPoolSize：线程池允许的最大线程池数量
keepAliveTime：线程数量超过corePoolSize，空闲线程的最大超时时间
unit：超时时间的单位
workQueue：工作队列，保存未执行的Runnable 任务
threadFactory：创建线程的工厂类
handler：当线程已满，工作队列也满了的时候，会被调用。被用来实现各种拒绝策略。

线程池参数设置：CPU 密集型任务--线程数= N（CPU 核心数）+1；I/O 密集型任务：线程数= N（CPU 核心数）* 2

排查死锁

通过jdk工具jps、jstack排查死锁问题；通过jdk提供的工具jconsole排查死锁问题。避免死锁：正确的顺序获得锁；超时放弃。

了解Java中的软引用、弱引用、虚引用的适用场景以及释放机制

强引用是使用最普遍的引用。如果一个对象具有强引用，那垃圾收器绝不会回收它。

软引用是用来描述一些还有用但并非必须的对象。对于软引用关联着的对象，在系统将要内存溢出之前，将会把这些对象进行第二次回收。

弱引用也是用来描述非必须对象的，他的强度比软引用更弱一些，被弱引用关联的对象，在垃圾回收时不论内存如何，这个对象都会被回收。

为一个对象设置虚引用关联的唯一目的就是能在这个对象被收集器回收时收到一个系统通知。

类加载:了解双亲委派机制
双亲委派机制是指当一个类加载器收到一个类加载请求时，该类加载器首先会把请求委派给父类加载器查询是否已经加载。只有在父类加载器找不到指定类时，子类加载器才会尝试自己去加载。

双亲委派机制的作用：防止重复加载同一个.class；保证核心.class不能被篡改。

了解BIO和NIO的区别、了解多路复用机制

BIO同步阻塞：一个连接一个线程，即客户端有连接请求时服务器端就需要启动一个线程进行处理，如果这个连接不做任何事情会造成不必要的线程开销。

NIO同步非阻塞：一个请求一个线程，但客户端发送的连接请求都会注册到多路复用器上，多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。

AIO异步：异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

多路复用机制：用户首先将需要进行IO操作的socket添加到select中，然后阻塞等待select系统调用返回。当数据到达时，socket被激活，select函数返回。用户线程正式发起read请求，读取数据并继续执行。

优点：用户可以在一个线程内同时处理多个socket的IO请求，用户可以注册多个socket，然后不断地调用select读取被激活的socket，即可达到在同一个线程内同时处理多个IO请求的目的。

GC：垃圾回收基本原理、几种常见的垃圾回收器的特性、重点了解CMS（或G1）以及一些重要的参数

GC垃圾回收机制，Java虚拟机进行垃圾回收的主要场所是堆，其次要场所是方法区；

Java将堆内存分为3大部分：新生代、老年代和永久代，其中新生代又进一步划分为Eden、S0、S1(Survivor)三个区

对象优先在Eden中分配，如果是大对象（很长的字符串数组）则可以直接进入老年代，当Eden中没有足够空间时，虚拟机将发生一次Minor GC，Minor GC非常频繁而且速度也很快。每一次MinorGC（年轻代GC），对象年龄就大一岁，默认15岁晋升到老年代；

Full GC是指发生在老年代的GC，当老年代没有足够的空间时即发生Full GC，发生Full GC一般都会有一次Minor GC；

Survivor的存在意义：就是减少被送到老年代的对象，进而减少Full GC的发生，Survivor的预筛选保证，只有经历16次Minor GC还能在新生代中存活的对象，才会被送到老年代。

刚刚新建的对象在Eden中，经历一次Minor GC，Eden中的存活对象就会被移动到第一块survivor space S0，Eden被清空；等Eden区再满了，就再触发一次Minor GC，Eden和S0中的存活对象又会被复制送入第二块survivor space S1（这个过程非常重要，因为这种复制算法保证了S1中来自S0和Eden两部分的存活对象占用连续的内存空间，避免了碎片化的发生）。S0和Eden被清空，然后下一轮S0与S1交换角色，如此循环往复。如果对象的复制次数达到16次，该对象就会被送到老年代中。

JVM如何判定一个对象是否应该被回收

可达性分析gcRoot：基本思路就是通过一系列可以做为root的对象作为起始点，从这些节点开始向下搜索。当一个对象到root节点没有任何引用链接时，则证明此对象是可以被回收的。以下对象会被认为是root对象；

标记-清除算法：从引用根节点开始标记所有被引用的对象；遍历整个堆，把未标记的对象清除。缺点：标记清除后会有大量的不连续的内存碎片；

复制算法：把内存空间划为两个相等的区域，每次只使用其中一个区域。垃圾回收时，遍历当前使用区域，把正在使用中的对象复制到另外一个区域中，清理原区域。缺点：可用内存缩成了一半；

标记-整理算法：从根节点开始标记所有被引用对象；遍历整个堆，清除未标记对象并且把存活对象“压缩”到堆的其中一块，按顺序排放。此算法避免了碎片问题和内存空间问题。

CMS（Concurrent Mark Sweep）收集器：基于标记-清除算法实现的，是一种老年代收集器

CMS的垃圾收集过程分为4步：

初始标记：需要“Stop the World”，初始标记仅仅只是标记一下GC Root能直接关联到的对象，速度很快。
并发标记：是主要标记过程，这个标记过程是和用户线程并发执行的。
重新标记：需要“Stop the World”，为了修正并发标记期间因用户程序继续运作而导致标记产生变动的那一部分对象的标记记录（停顿时间比初始标记长，但比并发标记短得多）。
并发清除：和用户线程并发执行的，基于标记结果来清理对象。
优点：并发收集，停顿时间低；

缺点：并发标记和清理，CPU资源非常敏感、收集过程中会产生浮动垃圾、标记-清除方式会产生内存碎片；

G1（Garbage-First）收集器：

G1收集器将新生代和老年代取消了，取而代之的是将堆划分为若干的区域，每个区域都可以根据需要扮演新生代的Eden和Survivor区或者老年代空间，仍然属于分代收集器，区域的一部分包含新生代，新生代采用复制算法，老年代采用标记-整理算法。

G1跟踪各个region里面的垃圾堆积的价值大小（回收所获得的空间大小以及回收所需时间的经验值），在后台维护一个优先列表，每次根据回收时间来优先回收价值最大的region。
