## 1. Java基础阶段考卷
### 1.1 面向对象的三大特性是什么？（6分）
```txt
封装、继承和多态
```


### 1.2 Java中重载和重写的区别是什么？（6分）

```txt
1. 重载是指发生在同一个类中，方法名必须相同，参数列表必须不同（类型、个数、顺序不同），与方法的返回值类型和访问修饰符无关；
2. 重写发生在父子类（或实现接口）中，方法名、参数列表必须完全相同；返回值类型必须相同，或是父类返回值类型的子类；访问修饰符不能比父类更严格；子类方法抛出的受检异常不能比父类更宽泛，运行时异常不受此限制。
```


### 1.3 说下抽象类和接口的区别？（6分）

```txt
1. 一个类只能继承一个抽象类（单继承），但可以实现多个接口（多实现）。
2. 接口中只能有public static final修饰的常量，抽象类中可以有普通成员变量
3. 接口中的方法默认是public abstract的（JDK8之后可以有default和static具体方法）；抽象类中可以有非抽象的普通方法。
4. 抽象类中有构造器（供子类初始化时调用），接口没有构造方法
```


### 1.4  Java基本数据类型有哪些?各占多少字节?（4分）

```txt
4类8种，int(4字节),long(8字节),byte(1字节),short(2字节),char(2字节),float(4字节),double(8字节),boolean(通常按1字节理解)
```

### 1.5 说下包装类自动装箱和拆箱的原理（6分）

```txt
装箱：编译器在编译时自动调用包装类的 valueOf() 方法，将基本类型转换为包装类型。

拆箱：编译器在编译时自动调用包装类的 xxxValue() 方法（如 intValue()、doubleValue()、longValue() 等），将包装类型转换为基本类型。
```

### 1.6 Integer缓存机制是怎么样的？（5分）

```txt
1. Integer类内部维护了一个IntegerCache静态内部类，默认缓存[-128,127]之间的Integer对象。
2. 自动装箱本质上会调用Integer.valueOf()，如果数值在缓存范围内，会直接返回缓存对象。
3. 缓存上限可通过JVM参数调整。
4. 如果超出缓存范围，valueOf()会创建新的Integer对象；如果显式使用new Integer()，无论是否在缓存范围内，都不会走缓存。
```

### 1.7 ==和equals的区别（6分）

```txt
==对于基本数据类型，比较值是否相等；对于引用数据类型，比较的是内存地址是否相同（即是否为同一个对象）。
equals()是Object类的方法。默认情况下它的实现就是==（比较地址）。但有很多类重写了equals()方法，用于比较对象的内容（属性值）是否相等。
```

### 1.8 String、StringBuilder、StringBuffer的区别（6分）

```txt
1. String底层拿final修饰的数组构成，是不可变的；StringBuilder和StringBuffer继承自AbstractStringBuilder，是可变的。
2. String因为不可变所以线程安全；StringBuffer的大多数方法加了synchronized同步锁，是线程安全的；StringBuilder没有加锁，是非线程安全的。
3. StringBuilder性能最高，StringBuffer次之，String最低（因为String每次拼接都会生成新对象）。
```

### 1.9 String为什么设计成不可变的（6分）

```txt
1. 只有字符串不可变，才能安全地实现常量池，多引用共享同一个字符串对象，节省内存空间。
2. 不可变对象天生就是线程安全的，可以在多线程环境中自由共享
3. String作为网络连接、文件路径、反射类名等核心API参数，不可变保证了参数在传递过程中不会被恶意篡改。
4. 因为不可变，其hash值也不会变，因此String对象在创建时可以缓存hash值，非常适合作为HashMap的key。
```

### 1.10  Java异常体系结构是怎么样的？（5分）

```txt
1. Java异常体系的根类是Throwable。它派生出两个重要子类：Error和Exception。
2. Error：代表JVM本身严重错误，程序无法处理。
3. Exception：代表程序可以捕获并处理的异常，它分为2种，运行时异常和检查型异常。
```

### 1.11 什么是反射?反射的应用场景有哪些？（5分）

```txt
1. 反射是java面向对象的核心机制，即做到万物皆对象，通过反射机制动态获取类的内部信息（如属性、方法、构造器、注解等），并能操作任意对象，它打破了封装性，允许程序在运行时动态加载类
2. 一些主流的框架（Spring、MyBatis）底层大量使用反射机制（如IoC容器创建对象、AOP切面动态代理）
3. JDBC加载数据库驱动
4. 获取和解析类上的自定义注解。
```

### 1.12  如何获取Class对象？（6分）
```txt
1. 通过类名（如：类名.class）
2. 通过对象（如：对象名.getClass()）
3. 通过全限定类名（如：Class.forName("类的包路径全名称")）
4. 通过类加载器（如：ClassLoader.loadClass("<类的全限定名称>")）
```

### 1.13  LinkedList和ArrayList的区别?（6分）

```txt
1. ArrayList底层是动态数组，LinkedList底层是双向链表。
2. ArrayList存储结构是顺序的，可以通过下标偏移量实现快速查找（O(1)）；LinkedList必须从头或尾部遍历，查询速度慢（O(n)）。
3. ArrayList在尾部插入快，但在中间或头部插入/删除时需要移动大量元素，效率低；LinkedList在已知位置进行插入/删除操作只需要修改指针，不需要移动元素，相对较快。
4. ArrayList结尾可能有预留空间；LinkedList的每个节点都需要存储数据以及前后节点的指针，单个元素占用的内存比ArrayList大。
```

### 1.14 ArrayList的扩容机制是怎么样的？（6分）
```txt
1. 使用无参构造器创建ArrayList时，底层初始是空数组。
2. 第一次调用add()添加元素时，数组会初始化为默认容量10。
3. 后续添加元素导致容量不足时，会扩容为旧容量的1.5倍左右。
4. 扩容底层通过Arrays.copyOf()创建新数组并复制旧数组元素。
```
### 1.15 HashMap在jdk1.7和jdk1.8区别有哪些？（6分）
```txt
1. 底层数据结构的改变，1.7是数组+单向链表，1.8是数组+单向链表+红黑树
2. 1.7链表使用头插法，1.8链表改用尾插法
3. 1.7的hash运算复杂，1.8简化了hash运算（只做了一次高16位异或低16位）
4. 1.7扩容后需要重算所有元素的hash并分配位置，1.8利用巧妙的逻辑，元素要么停留在原位置，要么移动“原位置+旧容量”的位置，提升了扩容效率。
```
### 1.16 HashMap链表红黑树转换的阈值是怎么样的？（5分）
```txt
1. 当同一个桶中的链表节点数达到8，并且数组长度大于等于64时，链表会转换为红黑树。
2. 如果数组长度小于64，会优先扩容而不是转换成红黑树。
3. 当红黑树节点数量小于等于6时，会退化为链表。
```
### 1.17 HashMap的扩容机制是怎么样的？（6分）
```txt
HashMap 默认初始容量16，默认负载因子0.75。当元素数量超过阈值（容量×负载因子，默认12）时触发扩容，每次扩容为原容量的2倍。JDK8 的扩容优化：因为容量始终是2的幂，扩容后元素的新位置要么在原索引，要么在原索引+旧容量，不需要重新计算hash，效率更高。
```

---

## 2 MySQL阶段考卷

### 2.1 MySQL 中 InnoDB 和 MyISAM 的区别有哪些？并说明各自的适用场景（5 分）
```txt
1. InnoDB的锁粒度最小是行锁，支持事务和外键，可以存在聚簇索引和非聚簇索引，适用场景读/写都存在的场景，MySQL默认就是InnoDB
2. MyISAM的锁粒度最小是表锁，不支持事务和外键，只能存在非聚簇索引，适合只读的场景，以及大数据查询场景。
```
### 2.2 MySQL 的索引从物理存储结构上分为哪些类？请简述它们的区别（4 分）
```txt
主要分为 聚簇索引 和 非聚簇索引。
1. 聚簇索引，聚簇索引一般跟主键值绑定是唯一的聚簇索引，聚簇索引按照叶子节点存储所有数据内容，按主键ID双向链表进行存储，如果主键不是自增长情况下可能会出现叶分裂状况。
2. 非聚簇索引，一般用于二级索引的查询，其索引排序按照二级索引的值自然排序，叶子节点只存储二级索引以及主键的值，如果出现索引不覆盖的情况需要进行回表，新增数据时可能会出现叶分裂的情况。
```
### 2.3 为什么 MySQL (InnoDB) 选择使用 B+Tree 而不是 BTree？（6 分）
```txt
1. B树的存储会慢慢的导致树结构的加深，因为B树的存储是键和值一起进行存储的，B树的叶子节点不是双向链表，每次范围查询会重新从根节点一层层遍历，大量增加系统I/O的次数。
2. B+Tree 非叶子节点只存 key 和指针，不存完整数据，因此单个节点能存更多 key，树的扇出更高、层级更低，磁盘 IO 次数更少；B+Tree 的数据都存放在叶子节点，叶子节点之间通过有序链表连接，范围查询只需要定位起点后顺序遍历叶子节点，效率更高。
3. 相比 BTree，B+Tree 更适合数据库索引的等值查询、范围查询和磁盘页读取模型。
```
### 2.4 什么是“回表”？什么是“索引覆盖”？（5 分）
```txt
回表：
	通过非聚簇索引查询数据时，由于叶子节点只存了“索引列的值”和“主键ID”，如果你的查询语句还想要获取其它字段，MySQL就必须拿着找到的主键ID，回到聚簇索引的B+树中再查一遍，从而拿到整行数据的过程。

索引覆盖：
	索引覆盖是一种极高的SQL优化状态，查询需要的字段都能从索引中直接获取，不需要再去回表。
```
### 2.5 什么是联合索引的“最左前缀原则”？请举例说明。（5 分）
```txt
1. 在MySQL使用联合索引时，索引的匹配必须从最左边开始，并且不能跳过中间的列；
2. 以(a, b, c)为例，首先按a大小进行排序，当匹配到a值之后，再按b进行大小排序，同理，最后按c值进行排序；
3. 假设创建了联合索引idx_a_b_c(a, b, c)
	- 完全匹配（走全索引）：WHERE a = 1 AND b = 2 AND c = 3
	- 部分匹配（走部分索引）： WHERE a = 1 AND b = 2
	- 断层匹配（最左匹配失效）： WHERE a = 1 AND c = 3 （这里在MySQL 5.6后会利用索引下推优化）
	- 范围查询阶段： WHERE a = 1 AND b > 2 AND c = 3 （a,b走索引，但是b用了范围查询，导致后续c无法走索引）
```
### 2.6 什么情况会导致索引失效？最少说五种情况（5 分）
```txt
1. 对于组合索引没有遵循最左匹配原则
2. 索引列上使用了函数或运算
3. 使用了否定或非等值操作符
4. 右侧或全模糊匹配（Like开头）
5. 使用了OR连接索引的不同字段
6. 被范围查询中断
7. 隐式类型转换导致索引失效（最隐蔽的失效情况之一）
```
### 2.7 什么是“索引下推”？（5 分）
```txt
1. 索引下推（ICP）是 MySQL 5.6 引入的优化。
2. 在使用联合索引时，MySQL 会尽量在存储引擎层根据索引中的字段先过滤数据，减少回表次数。
3. 没有索引下推时，需要先根据索引找到大量主键，再逐条回表，最后由 Server 层过滤。
4. 索引下推的核心价值是减少回表次数。
```
### 2.8 使用 EXPLAIN 分析 SQL 时，type 字段有哪些常见值？（5 分）
```txt
const（通过主键或唯一索引） / eq_ref / ref（使用普通索引匹配多行）   
					   --性能优秀
range                  --可以接受（索引范围扫描）
index                  --全索引扫描（一般算慢）
ALL                    --全表扫描（灾难，必须优化）
```
### 2.9 EXPLAIN 结果中，Extra 字段出现 Using filesort 和 Using temporary 分别意味着什么？（5 分）
```txt
1. Using filesort：无法直接利用索引完成排序，需要额外排序；不一定落盘，但排序成本较高。
2. Using temporary：执行中需要临时表，常见于 GROUP BY、DISTINCT、ORDER BY、UNION。
3. 优化：让 WHERE、ORDER BY、GROUP BY 匹配联合索引；减少查询字段；使用覆盖索引；必要时拆 SQL。
4. EXPLAIN 重点看 type、key、rows、filtered、Extra，避免 ALL + Using filesort/Using temporary。
```
### 2.10 事务有什么特性？分别由什么保证的？（7 分）
```txt
ACID：
1. 原子性：事务要么全成功，要么全回滚，主要由 Undo Log 保证。
2. 一致性：事务前后数据满足约束和业务规则，由 A/I/D 以及约束共同保证。
3. 隔离性：并发事务互不干扰，主要由锁机制和 MVCC 保证。
4. 持久性：提交后数据不因宕机丢失，主要由 Redo Log 保证。
```
### 2.11 并发事务之间会出现什么问题？分别是什么现象？如何能解决这些问题？（6 分）
```txt
1. 脏读：读到其他事务未提交数据；
		解决方式：通过读已提交及以上隔离级别可解决。
		
2. 不可重复读：同一事务多次读同一行结果不同；
		解决方式：通过可重复读及以上隔离级别可解决。
		
3. 幻读：同一事务中按条件多次查询，发现记录数量变化。
		解决方式：可以通过间隙锁、临键锁或串行化解决。
		
4. 丢失更新：并发更新互相覆盖；
		解决方式：用行锁、乐观锁版本号、唯一约束或串行化解决。
```
### 2.12 MySQL 的隔离级别有哪些？默认隔离级别是什么？该级别下解决了哪些问题，还存在什么问题？（6 分）
```txt
1. 读未提交(Read Uncommitted)：可能脏读、不可重复读、幻读。
2. 读已提交(Read Committed)：解决脏读，仍可能不可重复读、幻读。
3. 可重复读(Repeatable Read)：InnoDB 默认级别，解决脏读、不可重复读；快照读靠 MVCC，当前读靠 Next-Key Lock 抑制幻读。
4. 串行化(Serializable)：串行执行，隔离性最强，性能最低。

MySQL InnoDB 默认隔离级别是可重复读(RR)该级别解决了脏读、不可重复读问题。

在 InnoDB 中，通过 MVCC 和 临键锁(Next-Key Lock)，在很多场景下也能避免幻读，但普通快照读和当前读机制不同，严格来说仍需结合锁机制理解
```
### 2.13 什么是 MVCC ？（5 分）
```txt
MVCC 是多版本并发控制，用来提升读写并发。
1. 它通过保存数据的多个历史版本，让读操作不用加锁也能读取到事务开始时或语句开 始时可见的数据版本。
2. 查询时生成 Read View，判断哪些事务版本可见。
3. 当前版本不可见时，沿 Undo Log 找历史可见版本。
4. 读已提交(RC)的隔离级别下，每次快照读生成新 Read View；可重复读(RR)隔离级别下，同一事务复用第一次快照读的 Read View。
```
### 2.14 说下 Redo Log、Undo Log 和 Binlog 的作用分别是什么（6 分）
```txt
1. Redo Log：InnoDB 物理日志，记录页修改，用于崩溃恢复，保证持久性。
2. Undo Log：记录修改前版本，用于事务回滚和 MVCC，保证事务的原子性。
3. Binlog：Server 层逻辑日志，用于主从复制、数据恢复、审计。
作用： 
Redo 保证提交不丢，Undo 保证失败可回滚，Binlog 保证可复制和按时间点恢复。
```
### 2.15 什么是“两阶段提交”？（6 分）
```txt
两阶段提交用于保证 Redo Log 和 Binlog 一致。

第一阶段：写入 Redo Log，并标记为 prepare 状态，之后写 Binlog：Server 层写入并刷盘 Binlog。
第二阶段：写入 Binlog，然后把 Redo Log 标记为 commit 状态。

宕机恢复时，prepare 状态会检查 Binlog 是否完整；完整则提交，不完整则回滚，保证主库恢复和从库复制的一致性
```
### 2.16 InnoDB 的行级锁有哪些？（5 分）
```txt
1. 记录锁(Record Lock)：锁定某条索引记录。
2. 间隙锁(Gap Lock)：锁定索引记录之间的间隙，防止插入。
3. 临键锁(Next-Key Lock)：记录锁 + 间隙锁，RR 下用于防幻读。
4. 插入意向锁(Insert Intention Lock)：插提高并发插入效率。
5. 行锁加在索引上；不走索引可能锁住大量记录，效果接近锁表。
```
### 2.17 MySQL 主从复制的工作原理是怎么样的？（6 分）
```txt
1. 主库提交事务后写 Binlog。
2. 从库 IO 线程请求主库 Binlog。
3. 主库 Dump 线程发送 Binlog。
4. 从库 IO 线程写 Relay Log。
5. 从库 SQL 线程重放 Relay Log。
6. 延迟可通过并行复制、减少大事务、提升从库配置、强一致读主库解决。
```
### 2.18 说下 SQL 调优的完整流程。要求包含：为什么要调优、如何获取需要调优的 SQL、以及具体的调优手段。（10 分）
```txt
1. 目的：降低响应时间、减少 CPU/IO/锁等待、提升吞吐。
2. 获取 SQL：慢查询日志、监控、performance_schema、show processlist、业务日志。
3. 分析：EXPLAIN/EXPLAIN ANALYZE 看 type、key、rows、filtered、Extra。
4. 索引：建立合适联合索引，遵守最左前缀，使用覆盖索引，避免函数、计算、隐式转换、左模糊。
5. SQL：避免 select *，深分页用延迟关联/游标翻页，OR 可改 UNION，大查询拆分。
6. 表结构：字段类型合理，冷热拆分，必要时分库分表。
7. 事务：缩短事务时间，固定更新顺序，减少锁冲突。
8. 闭环：压测或灰度验证，观察慢日志和资源指标。
```

---
## 3 Redis阶段考卷

### 3 .1 Redis为什么快？（6分）
```txt
1. Redis是NoSQL型数据库结构，且基于内存操作，这是高速的基础，并且支持RDB和AOF的持久化机制。
2. Redis 快的原理是命令执行单线程 + I/O多路复用机制，单线程避免了锁竞争和CPU上下文切换，I/O多路复用避免了I/O阻塞。Redis 6.0 后网络读写可多线程，但命令执行仍保持单线程。
```
### 3 .2 Redis 有哪些核心数据结构？分别适用什么场景？（6 分）
- **单个简单值、计数、锁** $\rightarrow$ **String**
- **结构化对象、属性局部更新** $\rightarrow$ **Hash**
- **有序列表、队列** $\rightarrow$ **List**
- **去重、集合运算（交、并、差）** $\rightarrow$ **Set**
- **排行榜、范围查找、权重** $\rightarrow$ **Zset**
- **海量去重统计（允许微弱误差）** $\rightarrow$ **HyperLogLog**
- **海量状态打标（0/1 状态）** $\rightarrow$ **Bitmap**
- **位置与距离** $\rightarrow$ **Geo**
```txt
Redis的核心数据结构分为：
基础类型：String,List,Hash,Set,ZSet
高级类型：Bitmaps,HyperLogLog,GEO,Stream(Redis 5.0引入)
使用场景：
	基础类型：
		1. String, 缓存热点数据，计数器，分布式锁，共享Session
		2. Hash, 对象存储
		3. List, 队列消息
		4. Set, 无序去重集合
		5. ZSet, 带权重的队列消息，延时队列，排行榜
	高级类型：
		6. Bitmaps, 签到系统，用户在线状态，活跃用户统计，用户标签，权限系统，推送状态
		7. HyperLogLog, 网站UV统计，IP去重，搜索词统计，统计日活、月活用户数量，推荐去重，统计注册用户总量（返回不重复元素的近似数量）
		8. GEO, 存储位置，计算距离，范围搜索
```
### 3.3 RDB 和 AOF 两种持久化机制有什么区别？（6 分）
```txt
RDB，其COW机制保证不阻塞主进程下高效的生成快照，定期将内存中的数据状态保存为紧凑二进制文件（文件体积小），快照保存，安全性较低，宕机数据会丢失修改内容，文件恢复速度极快，性能上主要是fork（）子进程是短暂阻塞，持久化过程由子进程在后台完成，对主进程I/O影响小。
AOF，主要是操作日志，记录每次执行的写命令会追加到日志文件中（文件体积会随着时间推移持续膨胀），通过fsync策略刷盘，数据安全性较高，文件恢复速度较慢，需要重新执行日志中的写命令重建数据状态，性能主要取决于刷盘频率，高并发时会给磁盘I/O带来一定压力。
```
### 3.4 Redis 缓存穿透、击穿、雪崩分别是什么现象？分别如何解决？（6 分）
```txt
缓存穿透：
	请求的数据在缓存和数据库中都不存在。导致每次请求都直接打到数据库，如果有恶意攻击，数据库会因承受不住流量而瘫痪。
解决方法：
	1. 在接口层加强入参校验，
	2. 在访问缓存前，用布隆过滤器判断 Key 是否存在，不存在则直接拦截，
	3. 使用空值缓存，
	4. 异步回填，对于可能存在的key，使用消息队列(RocketMQ、Kafka)异步回填缓存，结合布隆过滤器动态更新。
	5. 进行IP限流，对异常高频的IP或用户添加黑名单或限流，防止恶意穿透。

缓存击穿：
	某一个并发量极高的热点Key在过期的瞬间，大量请求同时涌入。由于缓存失效，这些请求会同时去数据库查询并重建缓存，导致数据库瞬间压力暴增。
解决办法：
	1. 使用分布式锁 + 降级和限流 这两个操作是为了兜底出现了缓存击穿的时候 保护我们的微服务不会出现级联失败（雪崩）；
预防方法：
	2. 多级缓存，结合本地缓存和Redis缓存，先查本地缓存，减少对Redis的压力；
	3. 热Key预加载，提前识别热门key（通过日志分析或监控），在系统启动或低峰时期预加载到缓存，并设置较长的过期时间或永不过期；
	4. 逻辑过期：不设置真实的过期时间，而是将数据存入缓存时附加一个逻辑过期字段（如过期时间戳）。查询时检查是否逻辑过期，如果过期则异步线程重建缓存，主线程返回旧数据，确保不阻塞。

缓存雪崩：
	大量不同的 Key 在同一时间大面积过期失效，Redis 整个集群发生宕机。导致原本由缓存承担的巨量流量瞬间全部涌向数据库，引发数据库级联崩溃。
解决方法：
	降级限流：集成Sentinel或Hystrix，对高并发key添加限流阈值，超出时直接返回默认值或降级响应（兜底策略）。
预防措施：
	1. 多级缓存与热备份：引入L1和L2多级缓存，L1设置短TTL，L2设置长TTL；或使用热备份缓存（其它Redis集群），雪崩时切换。
	2. 随机过期时间： 为key设置过期时间添加随机偏移（如TTL + random（0，600）秒），避免大量key同时过期；
	3. 预热缓存： 系统启动时预加载热门数据到缓存，或使用定时任务定期刷新即将过期的key。
	4. 互斥锁重建，使用分布式锁确保过期key重建时只有一个线程访问DB，其它线程等待或返回旧数据。
	5. 熔断与限流：集成限流组件，对整体请求限速；使用Hystrix熔断器，当DB压力大时自动降级返回默认数据。
	6. 提高硬件架构等级：部属Redis Cluster或Sentinel，确保Master宕机时快速故障转移，故障转移需要集群中多数 Master 节点参与判断，否则可能无法完成选主；结合消息队列缓冲请求，异步处理DB查询。
```
### 3.5 Redis 分布式锁如何实现？有哪些注意事项？（6 分）
```txt
常见实现方式：
`SET key value NX PX 30000`
当key不存在才设置成功（获取锁成功），key存在则设置失败（获取锁失败）

注意点：
1. 死锁：必须设置过期时间。
2. 锁误删：value使用唯一标识，释放锁时先判断再删除。
3. 释放原子性：使用Lua脚本校验value并删除。
4. 锁续期：可使用Redisson看门狗机制。
5. 可重入：Redisson支持可重入锁。
6. 阻塞等待：控制等待时间和重试次数。
7. 主从一致性：普通Redis锁在主从切换时可能丢锁，RedLock通过多个独立Redis实例加锁，多数实例成功才认为加锁成功。
```
### 3.6 Redis 过期键的删除策略有哪些？（6 分）
```txt
1. 定时删除
   为每一个Key设置过期时间，时间一到，定时器立即触发删除。
2. 惰性删除
   不主动删除过期键，而是访问键时自动判断是否过期，如果过期则删除并返回nil。
3. 定期删除
   Redis会周期性的（默认每秒10次）随机抽取一批设置了过期时间的Key（默认为20个），检查并删除其中已过期的Key，如果删除的过期Key数量超过总抽样数的25%（即5个），则认为过期Key较多，会再次重复此过程，直到比例低于25%，或是达到最大运行时间（默认25ms）。
```
### 3.7 Redis 内存淘汰策略有哪些？（6 分）
```txt
1. 不淘汰策略
   当内存不足时，对于新的写入操作，Redis会直接拒绝并返回错误，读取操作能正常执行。
	   
2. 全Key淘汰策略
	- allkeys-lru：从所有Key中，淘汰最近最少使用的Key。
	- allkeys-lfu：从所有Key中，淘汰最不经常使用的Key。
	- allkeys-random：从所有Key中，随机淘汰Key。
   
3. 过期Key淘汰策略
	- volatile-lru：从设置了过期时间的Key中，淘汰最近最少使用的过期Key。
	- volatile-lfu：从设置了过期时间的Key中，淘汰最不经常使用的Key。
	- volatile-random：从设置了过期时间的Key中，随机选择Key进行淘汰。
	- volatile-ttl：从设置了过期时间的Key中， 淘汰TTL最短的Key。
```
### 3.8 Redis 主从复制的原理和流程是什么？（6 分）
```txt
原理：
	1 通过设置一个master（主节点） + N个Slave（从节点），Master负责读写，Slave负责读。
	2 主从复制过程：
		a. runid是每台服务器每次运行的身份识别码，运行id在每台服务器启动时自动生成
		b. 复制缓冲区，是一个先进先出(FIFO)的队列，用于存储服务器执行过的命令
	
主从同步的流程：
	1. Slave节点会主动向Master节点发送同步数据的请求
		a. 如果是第一次同步，发送的命令是 PSYNC ? -1 （?代表第一次同步，-1代表同步所有数据）
		b. 如果不是第一次同步，发送的命令是 PSYNC <replication_id> <offset>（主节点的replication id，上次同步偏移量位置offset）
	2. Master节点收到Slave的同步数据请求后，如果是第一次同步，会将返回自己内存中的数据生成RDB文件（通过bgsave生成快照，不阻塞主进程），以及自己的runid和当前自己复制缓冲区中的偏移量信息到Slave节点。
	3. Slave收到Master返回的同步数据，然后进行RDB的恢复，同时保存Master的runid和offset
	4. Slave还会定期向Master发送同步数据请求，发送命令是psync2 <runid> <offset>
	5. Master接收同步数据请求后会进行判断
		a. 先判断Slave传递过来的runid是不是自己的runid
			i. 如果是自己的runid再去比对offset
			ii. 如果不是自己的runid会再次进行全量复制
		b. 然后判断offset是否在当前的复制缓冲区内
			i. 如果在旧直接从offset的位置开始，返回到最新的数据，同时把最新的offset返回给Slave
			ii. 如果不在说明Slave已经很长时间没有给Master进行数据同步了，复制缓冲区中的数据已经丢失了部分，会再次触发全量复制。
	6. 定期重复第4步和第5步
```
### 3.9 Redis 哨兵模式的核心功能和工作机制？（6 分）
```txt
哨兵模式的核心功能：
	1. 监控
	2. 故障判断
	3. 自动故障转移
	4. 通知客户端。
工作机制：
1. 哨兵通过 PING 检测节点状态。
2. 多个哨兵之间通过发布订阅机制交换信息。
3. 当主节点被判断为客观下线后，哨兵之间选举 Leader。
4. Leader 哨兵选择一个合适的从节点提升为新主节点。
5. 其他从节点重新复制新的主节点。
6. 原主节点恢复后，会被降级为新主节点的从节点。
```
### 3.10 Redis Cluster 集群的分片原理和高可用机制是怎么样的？（6 分）
```txt
Redis Cluster 通过分片实现数据水平扩展，通过主从复制实现高可用。
分片原理：
1. Redis Cluster 将数据划分为 16384 个哈希槽。
2. 每个 key 通过 CRC16(key) % 16384 计算所属槽位。
3. 每个主节点负责一部分槽位。
4. 客户端访问 key 时，会根据槽位找到对应节点。
5. 如果请求发错节点，Redis 返回 MOVED 或 ASK 重定向。

高可用机制：
1. 每个主节点可以配置一个或多个从节点。
2. 主节点宕机后，集群会从它的从节点中选举一个新的主节点。
3. 节点之间通过 Gossip 协议交换状态信息。
4. 当多数主节点认为某个主节点不可用时，会触发故障转移。
5. 如果某个主节点宕机且没有可用从节点，该主节点负责的槽不可用，集群可能进入不可用状态。

Redis Cluster 适合大数据量、高并发、需要水平扩容的场景。
```
### 3.11 如何保证 Redis 缓存与数据库的一致性？（6 分）
```txt
1. 常用 Cache Aside 模式：
  1. 读时先查缓存，未命中查数据库并写入缓存；
  2. 写时先更新数据库，再删除缓存。
2. 不推荐直接更新缓存，因为并发下缓存容易写入旧值，推荐先更新数据库再更新缓存。
3. 通过延迟双删降低并发读写导致缓存脏数据的概率，但不能保证强一致。
4. 设置缓存过期时间，即使缓存删除失败，也能通过 TTL 最终过期。
5. 对一致性要求较高的场景，可以使用 Canal 监听 binlog 异步删除或更新缓存。
6. 删除缓存失败要通过重试、消息队列或监听 binlog 进行补偿，提高可靠性。
```
### 3.12 Redis 大 Key、热 Key 问题如何识别和解决？（6 分）
```txt
1. 大 Key：单个 key 占用内存过大，或集合元素过多；会导致网络慢、阻塞删除、复制压力大。
2. 热 Key：访问频率极高；会导致单节点 CPU/带宽/连接瓶颈。

3.识别：
	1. Redis 大 Key识别方式：
  		1. 使用Arthas诊断
  		2. 使用Bigkeys命令查找
  	2. Redis 热 Key 识别方式：
  		1. 业务的预测哪一个是热key
  		
4. 大 Key 解决：拆分、压缩、集合分片、UNLINK 异步删除、避免阻塞。
5. 热 Key 解决：本地缓存、多级缓存、Key 分片、读写分离、预热、限流降级。
```
### 3.13 Redis 事务的实现机制和特点是什么？（6 分）
```txt
1. 通过 MULTI、EXEC、DISCARD、WATCH 实现。

执行流程：
1. 使用 MULTI 开启事务。
2. 后续命令不会立即执行，而是进入命令队列。
3. 使用 EXEC 提交事务，Redis 按顺序执行队列中的命令。
4. 使用 DISCARD 可以取消事务。
5. 使用 WATCH 可以监听 key，实现乐观锁。

特点：
1. Redis 事务中的命令按顺序执行。
2. 执行过程中不会被其他客户端命令插入。
3. Redis 事务不支持回滚。
4. 如果命令入队时语法错误，整个事务不会执行。
5. 如果执行时发生运行错误，其他命令仍会继续执行。
6. 配合 WATCH 可以实现 CAS 乐观锁。

Redis 事务保证的是命令队列的顺序执行和隔离性，但不具备传统数据库事务的完整 ACID 能力。
```
### 3.14 Redis 布隆过滤器的原理和使用场景？（6 分）
```txt
布隆过滤器是一种空间效率很高的概率型数据结构，用于判断一个元素是否可能存在。

原理：
1. 底层是一个位数组。
2. 添加元素时，通过多个哈希函数计算多个位置。
3. 将这些位置的 bit 设置为 1。
4. 查询元素时，也计算多个位置。
5. 如果这些位置都为 1，说明元素可能存在。
6. 如果有任意位置为 0，说明元素一定不存在。

使用场景：
1. 防止缓存穿透。
Redis 可通过 RedisBloom 模块使用布隆过滤器。
```
### 3.15 Redis 单线程为什么还能实现高并发？（6 分）
```txt
Redis 的核心命令执行是单线程，但仍然能高并发。

原因包括：
1. Redis 操作主要基于内存，读写速度非常快。
2. 使用 I/O 多路复用机制，可以同时处理大量客户端连接。
3. 单线程避免了线程切换和锁竞争开销。
4. Redis 数据结构设计高效，例如 Hash、跳表、压缩列表等。
5. 命令执行通常很快，大多是 O(1) 或 O(logN)。
6. Redis 6.0 之后引入多线程 I/O，用于网络读写，但命令执行仍主要是单线程。

所以 Redis 的高性能来自内存操作、高效数据结构、事件驱动模型和避免多线程锁竞争。
```
### 3.16 Redis 主从延迟问题如何解决？（8 分）
```txt
主从延迟是指主节点写入后，从节点没有及时同步，导致读从库时读到旧数据。
产生原因：
1. 主节点写入压力过大。
2. 网络延迟或网络抖动。
3. 从节点性能不足。
4. 从节点正在执行大查询、阻塞命令或持久化。
5. 主从之间复制缓冲区压力大。
6. 大 key 写入或删除导致同步变慢。
解决方案：
1. 监控复制延迟
	通过 INFO replication 查看 master_repl_offset 和 slave_repl_offset 差值。
2. 读写策略优化
	对一致性要求高的请求读主库，对一致性要求低的请求读从库。
3. 避免大 key
	拆分大 key，删除时使用 UNLINK，避免阻塞复制。
4. 优化网络
	主从节点尽量部署在同机房、同可用区，减少网络延迟。
5. 提升从节点性能
	避免从节点机器资源不足，合理配置 CPU、内存和磁盘。
6. 减少阻塞命令
	避免在从节点执行 KEYS、大范围 SMEMBERS、大 Lua 脚本等慢操作。
7. 使用合理的复制配置
	设置复制积压缓冲区，避免短暂断线后全量复制。
8. 业务层处理延迟
	写操作后短时间内强制读主库，或者使用版本号、时间戳判断数据新旧。
9. 使用哨兵或 Cluster 保证高可用
	防止主节点故障时长期不可用，但要注意故障切换后仍可能有短暂数据不一致。

核心思路是：减少主从同步压力、避免阻塞、提升网络和机器性能，并在业务上区分强一致读和最终一致读。
```
---


## 4 JUC阶段考卷

### 4.1 创建线程有哪些方式？哪种方式最佳？为什么？（6分）
```txt
1. 继承 Thread。
2. 实现 Runnable。
3. 实现 Callable + FutureTask。
4. 使用线程池 ExecutorService。
5. 实际开发推荐线程池：复用线程、控制并发数、统一命名、监控、异常处理和拒绝策略。
6. 不推荐大量直接 new Thread，容易资源耗尽。
```

### 4.2 线程池的核心参数有哪些？任务提交到线程池的执行流程是怎样的？（8分）
```txt
核心参数：corePoolSize、maximumPoolSize、keepAliveTime、unit、workQueue、threadFactory、handler。
流程：
1. 线程数小于 corePoolSize，创建核心线程执行。
2. 核心线程满，任务进入队列。
3. 队列满且线程数小于 maximumPoolSize，创建非核心线程。
4. 线程数达到 maximumPoolSize 且队列满，执行拒绝策略。
5. 非核心线程空闲超过 keepAliveTime 被回收。
```

### 4.3 线程池的拒绝策略有哪些？如何选择拒绝策略？（6分）
```txt
1. AbortPolicy：默认，直接抛异常，适合不能丢任务且要暴露问题。
2. CallerRunsPolicy：提交线程自己执行，形成反压。
3. DiscardPolicy：直接丢弃，适合低价值可丢任务。
4. DiscardOldestPolicy：丢弃最老任务，适合只关心最新任务。
5. 生产建议自定义：记录日志、告警、落库/MQ 补偿或触发限流。
```

### 4.4 JDK自带的线程池有哪些？分别有什么弊端？为什么推荐使用自定义线程池？（7分）
```txt
1. FixedThreadPool：无界队列，可能 OOM。
2. SingleThreadExecutor：单线程 + 无界队列，堆积风险高。
3. CachedThreadPool：最大线程数过大，高并发可能耗尽资源。
4. ScheduledThreadPool：定时任务队列可能堆积，异常处理不当会停止任务。
5. WorkStealingPool：适合 CPU 拆分任务，不适合普通业务 IO。
6. 自定义线程池能明确线程数、队列、命名、拒绝策略和监控。
```

### 4.5 什么是线程安全问题？如何能解决线程安全问题？（6分）
```txt
线程安全问题是多个线程并发访问共享可变数据时，原子性、可见性、有序性无法保证。
解决：
1. 避免共享：局部变量、不可变对象、无状态设计。
2. 加锁：synchronized、ReentrantLock、读写锁。
3. 并发工具：Atomic、LongAdder、ConcurrentHashMap、BlockingQueue。
4. 可见性：volatile、锁。
5. 数据层：乐观锁版本号、悲观锁。
```

### 4.6 ReentrantLock 和 synchronized 的区别有哪些？（6分）
```txt
1. synchronized 是 JVM 关键字，自动释放；ReentrantLock 是 API，需要 finally unlock。
2. 二者都可重入。
3. ReentrantLock 支持公平/非公平；synchronized 不支持指定公平性。
4. ReentrantLock 支持 tryLock、超时、可中断等待。
5. ReentrantLock 支持多个 Condition；synchronized 用 wait/notify。
6. 简单同步用 synchronized，复杂锁控制用 ReentrantLock。
```

### 4.7 synchronized 的锁升级过程是怎么样的？（6分）
```txt
1. 无锁：对象头未记录锁信息。
2. 偏向锁：偏向第一个进入的线程，减少 CAS；JDK15 后默认关闭并逐步废弃。
3. 轻量级锁：多个线程交替竞争，通过 CAS 自旋获取。
4. 重量级锁：竞争激烈或自旋失败后膨胀，线程阻塞/唤醒依赖 OS。
5. 升级通常单向：无锁 -> 偏向锁 -> 轻量级锁 -> 重量级锁。
6. JVM 还会做锁消除、锁粗化、自旋优化。
```

### 4.8 什么是AQS？（6分）
```txt
AQS 是 AbstractQueuedSynchronizer，JUC 同步器基础框架。
1. 用 volatile int state 表示同步状态。
2. 通过 CAS 修改 state。
3. 获取失败的线程封装为 Node 进入 CLH 队列等待。
4. 支持独占和共享模式。
5. ReentrantLock、Semaphore、CountDownLatch、ReadWriteLock 都基于 AQS。
6. 子类只需实现 tryAcquire/tryRelease 等模板方法。
```

### 4.9 什么是 CAS？CAS 的底层实现原理？Java中有哪些地方用到了CAS？CAS有什么弊端？如何解决？（8分）
```txt
1. CAS 是 Compare And Swap，比较并交换，是乐观锁思想。
2. 比较内存值和期望值，相等则更新，否则失败重试。
3. 底层依赖 CPU 原子指令，Java 通过 Unsafe/VarHandle 调用。
4. 使用场景：Atomic、LongAdder、AQS、ConcurrentHashMap。
5. ABA 问题：用版本号 AtomicStampedReference 解决。
6. 自旋开销高：用 LongAdder、锁或降低竞争解决。
7. 单变量限制：多变量一致性用锁、事务或封装对象引用。
```

### 4.10 什么是JMM？（5分）
```txt
JMM 是 Java Memory Model，定义线程如何通过主内存通信。
1. 每个线程有工作内存，共享变量读写需和主内存交互。
2. 解决原子性、可见性、有序性问题。
3. volatile、synchronized、final、Lock 都依赖 JMM 语义。
4. happens-before 用于判断操作间可见性。
5. JMM 屏蔽不同 CPU/系统的内存差异。
```

### 4.11 volatile关键字的作用？（5分）
```txt
1. 保证可见性：一个线程修改后其他线程能看到最新值。
2. 禁止指令重排序：通过内存屏障保证顺序。
3. 不保证复合操作原子性，例如 i++ 仍不安全。
4. 适合状态标记、开关、单例双重检查。
5. 需要原子更新时用 Atomic 或锁。
```

### 4.12 ThreadLocal 的实现原理是什么？用完之后为什么需要remove?（6分）
```txt
1. ThreadLocal 为每个线程保存独立变量副本。
2. 每个 Thread 内部有 ThreadLocalMap，key 是 ThreadLocal 弱引用，value 是实际数据。
3. get/set/remove 操作的是当前线程自己的 Map。
4. 线程池会复用线程，不 remove 可能导致数据串用和内存泄漏。
5. 建议在 finally 中 remove。
```

### 4.13 CompletableFuture有什么作用？适用于什么场景？（6分）
```txt
1. CompletableFuture 用于异步任务编排、回调、组合、聚合和异常处理。
2. 常用方法：supplyAsync、thenApply、thenCompose、thenCombine、allOf、exceptionally。
3. 适合多个远程调用并行、结果聚合、异步通知、任务流水线。
4. 相比 Future，不必阻塞 get，可以链式处理。
5. 生产建议传自定义线程池，并处理超时和异常。
```

### 4.14 Semaphore、CountdownLatch、CyclicBarrier分别适用于什么场景？（7分）
```txt
1. Semaphore：信号量，控制同时访问资源的线程数量，如限流、连接池许可。
2. CountDownLatch：一个或多个线程等待其他任务完成，只能用一次。
3. CyclicBarrier：一组线程互相等待，全部到达后继续，可重复使用。
4. CountDownLatch 偏“一个等多个”，CyclicBarrier 偏“多个互相等”。
5. CompletableFuture.allOf 可替代部分 CountDownLatch 场景。
```

### 4.15 ConcurrentHashMap是如何保证线程安全的？（7分）
```txt
1. JDK8 底层是数组 + 链表 + 红黑树。
2. 读操作大多无锁，依赖 volatile 可见性。
3. put 时桶为空用 CAS；桶不为空对桶头 synchronized。
4. 扩容时多线程协助迁移，ForwardingNode 标识迁移状态。
5. size 用 baseCount + CounterCell，降低竞争。
6. 链表过长且容量足够时树化。
7. 不允许 null，避免并发下语义歧义。
```

---

## 5 JVM阶段考卷

### 5.1 JVM是什么？（5分）
```txt
JVM 是 Java Virtual Machine，运行 Java 字节码的虚拟机。
1. 负责类加载、字节码验证、解释执行/JIT 编译、内存管理和 GC。
2. Java 源码编译成 class 后由 JVM 运行，实现跨平台。
3. 常见实现是 HotSpot。
4. 调优重点关注内存、GC、线程、JIT 和类加载。
```

### 5.2 类的加载流程是样的？（6分）
```txt
流程：加载、验证、准备、解析、初始化，之后是使用和卸载。
1. 加载：读取 class 字节流并生成 Class 对象。
2. 验证：校验格式、安全和语义。
3. 准备：为类变量分配内存并设默认值。
4. 解析：符号引用转直接引用。
5. 初始化：执行 <clinit>，完成 static 变量赋值和 static 代码块。
```

### 5.3 JVM内存模型是怎样的？哪些是线程私有的，哪些是线程共享的？栈的基本组成单位是什么？（6分）
```txt
运行时数据区：程序计数器、虚拟机栈、本地方法栈、堆、方法区。
1. 线程私有：程序计数器、虚拟机栈、本地方法栈。
2. 线程共享：堆、方法区/元空间、运行时常量池。
3. 堆存对象实例，是 GC 重点区域。
4. 方法区存类元信息、常量、静态变量等；JDK8 后主要为元空间。
5. 栈的基本单位是栈帧，包含局部变量表、操作数栈、动态链接、返回地址。
```

### 5.4 什么是双亲委派模型？为什么需要双亲委派模型？（6分）
```txt
双亲委派：类加载器先委托父加载器加载，父加载器无法加载时才自己加载。
作用：
1. 保护 JDK 核心类，防止自定义 java.lang.String 等替换核心类。
2. 避免重复加载，保证类唯一性。
3. 形成清晰加载层级，便于隔离和管理。
4. SPI、Tomcat、插件化场景可能打破双亲委派。
```

### 5.5 JVM中的类加载器有哪些？它们各自的作用是什么？（6分）
```txt
1. Bootstrap ClassLoader：加载 Java 核心类库。
2. Extension ClassLoader：JDK8 加载 jre/lib/ext；JDK9 后由平台类加载器替代。
3. Platform ClassLoader：JDK9 引入，加载平台模块。
4. Application ClassLoader：加载 classpath/module path 下应用类。
5. Custom ClassLoader：用于热部署、插件化、类隔离、加密 class 加载。
```

### 5.6 如何判断某个对象可以被回收？（6分）
```txt
主流 JVM 使用可达性分析。
1. 从 GC Roots 出发沿引用链查找，能到达的是存活对象。
2. 不可达对象可作为回收候选。
3. GC Roots 包括栈中引用、静态变量引用、常量引用、JNI 引用、被锁持有对象等。
4. finalize 存在一次自救机会，但不可靠且不推荐。
5. 引用计数法无法解决循环引用，HotSpot 不作为主要算法。
```

### 5.7 Java中四大引用是什么？分别有什么特点？（5分）
```txt
1. 强引用：只要存在就不会被 GC 回收。
2. 软引用：内存不足时回收，适合缓存。
3. 弱引用：发生 GC 时可能回收，如 ThreadLocalMap 的 key。
4. 虚引用：不能获取对象，用于回收跟踪，需配合 ReferenceQueue。
5. 强度：强 > 软 > 弱 > 虚。
```

### 5.8 什么是垃圾回收（GC）？常见的垃圾回收算法有几种？分别有什么优缺点？（6分）
```txt
GC 是 JVM 自动回收不再使用对象内存的机制。
1. 标记-清除：简单，但会产生碎片。
2. 标记-复制：无碎片、效率高，但浪费一部分空间。
3. 标记-整理：无碎片，但移动对象成本高。
4. 分代收集：按对象生命周期分新生代/老年代使用不同算法，是 HotSpot 常用思想。
```

### 5.9 堆分为哪些区域？每个区域的作用是什么？默认的比例分别是多少？（7分）
```txt
传统分代堆分为新生代和老年代。
1. 新生代：存放新对象，通常复制算法。
2. 老年代：存放长期存活对象或大对象。
3. 新生代分 Eden、S0、S1。
4. 默认 Eden:S0:S1 通常为 8:1:1，可用 -XX:SurvivorRatio 调整。
5. 默认新生代:老年代通常约 1:2，可用 -Xmn 或 -XX:NewRatio 调整。
6. JDK8 后元空间不属于堆。
```

### 5.10 聊聊CMS和G1的特点和区别？（7分）
```txt
1. CMS 目标是低停顿，主要回收老年代，使用标记-清除。
2. 流程：初始标记、并发标记、重新标记、并发清除。
3. CMS 缺点：内存碎片、并发阶段占 CPU、可能 Concurrent Mode Failure。
4. G1 将堆划分为 Region，按回收收益优先回收。
5. G1 可设置 MaxGCPauseMillis，停顿更可预测。
6. JDK9 后 G1 是默认收集器，更适合大堆和综合平衡。
```

### 5.11 内存泄漏和内存溢出的区别？分别可能是因为什么导致的？（6分）
```txt
1. 内存泄漏：对象不用了但仍被引用，GC 无法回收。
2. 内存溢出：内存不足，无法再分配，抛 OOM。
3. 泄漏长期积累可能导致溢出。
4. 泄漏原因：静态集合、ThreadLocal 未 remove、监听器未注销、资源未关闭、缓存无淘汰。
5. 溢出原因：堆小、大对象、请求堆积、元空间类过多、直接内存未释放、线程过多。
```

### 5.12 Full GC触发的原因有哪些？JVM调优可以调整哪些参数？（7分）
```txt
触发原因：老年代不足、晋升失败、元空间不足、System.gc、大对象进入老年代、CMS/G1 失败。
可调参数：
1. -Xms、-Xmx、-Xmn。
2. -XX:NewRatio、-XX:SurvivorRatio、-XX:MaxTenuringThreshold。
3. -XX:MetaspaceSize、-XX:MaxMetaspaceSize。
4. -XX:MaxGCPauseMillis、GC 收集器参数。
5. 调优前先分析 GC 日志和 dump，不要盲目加内存。
```

### 5.13 JVM调优中Prometheus是如何使用的？（7分）
```txt
1. 通过 Micrometer 或 JMX Exporter 暴露 JVM 指标给 Prometheus。
2. 监控堆/非堆、Eden/Survivor/Old、GC 次数和耗时、线程、类加载、CPU、接口耗时。
3. Grafana 展示趋势，观察 Full GC、Old 区增长、泄漏趋势。
4. 告警：Old 区高水位、Full GC 频繁、GC 暂停长、线程异常、元空间增长。
5. Prometheus 发现趋势，定位还需结合 GC 日志、jstack、jmap、MAT、arthas。
```

### 5.14 CPU百分百问题如何排查？（8分）
```txt
1. top 找 CPU 高的 Java 进程 PID。
2. top -Hp PID 找高 CPU 线程 TID。
3. 将 TID 转 16 进制：printf "%x\n" TID。
4. jstack PID 导出线程栈，搜索 nid。
5. 分析死循环、频繁 GC、锁竞争、自旋、正则回溯、序列化/加密等。
6. 若是 GC 线程高，继续看 GC 日志和对象分配。
7. 可用 arthas thread、dashboard、trace、profiler 火焰图辅助定位。
```

### 5.15 频繁Full GC问题如何排查？（8分）
```txt
1. 看 GC 日志，确认频率、耗时、触发原因和回收效果。
2. GC 后 Old 区仍高，排查泄漏或长期存活对象。
3. 导出 heap dump，用 MAT 看大对象和引用链。
4. 看对象分配速率，判断晋升是否过快。
5. 查缓存、静态集合、ThreadLocal、连接池、消息堆积。
6. 查元空间是否不足。
7. 根据结果调堆、新生代比例、晋升阈值、GC 收集器和停顿目标。
```

### 5.16 OOM问题如何排查？（8分）
```txt
1. 先确认 OOM 类型：heap、Metaspace、Direct buffer、native thread、GC overhead 等。
2. 开启 -XX:+HeapDumpOnOutOfMemoryError 保留现场。
3. 用 MAT/VisualVM 看 Dominator Tree、Leak Suspects、GC Roots。
4. 堆 OOM 查缓存、集合、大对象、批量查询、消息堆积。
5. Metaspace 查动态类、类加载器泄漏、热部署。
6. Direct Memory 查 Netty/NIO 堆外内存释放。
7. native thread 查线程池无界和系统限制。
```

---

## 6 Spring阶段考卷

### 6.1 Spring有哪些特点？（4分）
```txt
1. IoC：对象创建和依赖管理交给容器。
2. AOP：日志、事务、权限等横切逻辑解耦。
3. 声明式事务：注解或配置管理事务。
4. 生态完善：整合 MVC、JDBC、ORM、缓存、消息、测试。
5. 非侵入式：业务代码对框架依赖较低。
```

### 6.2 IoC容器有哪些作用？Bean的生命周期有哪些？（6分）
```txt
IoC 容器负责创建 Bean、依赖注入、作用域、生命周期、AOP 和事件。
生命周期：
1. 实例化。
2. 属性填充。
3. Aware 回调。
4. BeanPostProcessor 前置处理。
5. 初始化：@PostConstruct、InitializingBean、init-method。
6. BeanPostProcessor 后置处理，可能生成代理。
7. Bean 可用。
8. 销毁：@PreDestroy、DisposableBean、destroy-method。
```

### 6.3 Spring中依赖注入有哪些方式？（5分）
```txt
1. 构造器注入：适合必需依赖，推荐。
2. Setter 注入：适合可选依赖。
3. 字段注入：简单但不利于测试和依赖显式表达，不推荐核心业务使用。
4. 方法参数注入：常见于 @Bean 方法。
5. 推荐构造器注入 + final 字段。
```

### 6.4 Bean的作用域有哪些？核心业务Bean（如Service、Controller）通常使用哪种作用域？为什么？（5分）
```txt
1. singleton：默认单例。
2. prototype：每次获取新实例。
3. request：每个 HTTP 请求一个实例。
4. session：每个 Session 一个实例。
5. application：每个 ServletContext 一个实例。
6. websocket：每个 WebSocket 会话一个实例。
7. Service/Controller 通常用 singleton，因为一般无状态，性能更好；状态数据放局部变量或请求上下文。
```

### 6.5 Spring中Bean的装配方式有哪些？分别适用于什么场景？（6分）
```txt
1. XML：老项目集中配置。
2. 注解扫描：@Component/@Service/@Repository/@Controller，适合业务类。
3. Java Config：@Configuration + @Bean，适合第三方组件和复杂初始化。
4. 自动注入：@Autowired、@Resource、@Inject。
5. 条件装配：@Conditional、@Profile、@ConditionalOnClass 等。
6. Boot 常用注解扫描 + Java Config + 自动配置。
```

### 6.6 说下Spring Boot的自动装配原理？（6分）
```txt
1. @SpringBootApplication 包含 @EnableAutoConfiguration。
2. AutoConfigurationImportSelector 加载自动配置类。
3. Boot 2.x 从 spring.factories 读取；Boot 3.x 从 AutoConfiguration.imports 读取。
4. 通过 @ConditionalOnClass、@ConditionalOnMissingBean、@ConditionalOnProperty 判断是否生效。
5. @ConfigurationProperties 绑定配置文件。
6. 核心是约定大于配置：依赖存在、条件满足、用户未自定义时创建默认 Bean。
```

### 6.7 @Controller、@Service、@Repository、@Component有什么区别？说明各自的应用场景。（6分）
```txt
1. @Component：通用组件。
2. @Controller：Web 控制层，接收请求并返回响应。
3. @Service：业务逻辑层，承载业务流程和事务边界。
4. @Repository：数据访问层，可做持久层异常转换。
5. 本质都能注册 Bean，但语义不同，应按分层使用。
6. @RestController = @Controller + @ResponseBody。
```

### 6.8 什么是Spring的配置类？@Configuration和@Bean注解的作用分别是什么？（7分）
```txt
1. 配置类是用 Java 代码定义 Bean 和配置的类。
2. @Configuration 标记配置类，本身也注册为 Bean。
3. @Bean 标记方法返回对象为 Bean。
4. @Configuration full 模式会被 CGLIB 代理，保证 @Bean 方法间调用仍是单例。
5. @Component + @Bean 是 lite 模式，不保证方法间调用单例语义。
6. 适合第三方对象、复杂初始化和条件化装配。
```

### 6.9 什么是AOP？AOP的核心组件有哪些？（7分）
```txt
AOP 是面向切面编程，把日志、事务、权限、监控等横切逻辑从业务中抽离。
核心：
1. JoinPoint：连接点。
2. Pointcut：切点。
3. Advice：通知，具体增强逻辑。
4. Aspect：切面，切点 + 通知。
5. Target：目标对象。
6. Proxy：代理对象。
7. Weaving：织入过程。
```

### 6.10 实现AOP的动态代理有哪些方式？有什么区别？Spring默认使用哪种？（6分）
```txt
1. JDK 动态代理：基于接口，目标类必须实现接口。
2. CGLIB：基于继承生成子类，不要求接口，但不能代理 final 类/方法。
3. Spring 默认：有接口用 JDK，无接口用 CGLIB。
4. proxyTargetClass=true 可强制 CGLIB。
5. Spring AOP 是运行时代理，只增强 Spring 容器管理的 Bean 方法调用。
```

### 6.11 @Transactional注解有什么作用？核心属性有哪些？（5分）
```txt
@Transactional 用于声明式事务，Spring 通过 AOP 开启、提交、回滚事务。
核心属性：
1. propagation：传播行为。
2. isolation：隔离级别。
3. rollbackFor/noRollbackFor：回滚规则。
4. timeout：超时时间。
5. readOnly：只读提示。
6. transactionManager：事务管理器。
```

### 6.12 Spring事务传播行为有哪些？（7分）
```txt
1. REQUIRED：默认，有事务加入，无事务新建。
2. REQUIRES_NEW：挂起当前事务，新建事务。
3. SUPPORTS：有事务加入，无事务非事务执行。
4. NOT_SUPPORTED：非事务执行，有事务则挂起。
5. MANDATORY：必须有事务，否则异常。
6. NEVER：必须无事务，否则异常。
7. NESTED：有事务则嵌套事务，依赖保存点；无事务类似 REQUIRED。
```

### 6.13 @Transactional失效场景有哪些？（5分）
```txt
1. 方法非 public。
2. 同类内部调用绕过代理。
3. 对象不是 Spring Bean。
4. 异常被 catch 未抛出。
5. 默认不回滚受检异常，需 rollbackFor。
6. 数据库表不支持事务。
7. final/private/static 方法或代理方式不支持。
8. 新线程不在原事务上下文。
```

### 6.14 Spring和Spring Boot的区别是什么？（6分）
```txt
1. Spring 是基础框架，提供 IoC、AOP、事务、MVC 等。
2. Spring Boot 基于 Spring，提供自动配置、Starter、嵌入式服务器。
3. Spring 配置多；Boot 约定大于配置。
4. Boot Starter 管理依赖组合。
5. Boot 可 jar 包直接启动。
6. Boot 不替代 Spring，而是简化 Spring 应用搭建、配置、运行和监控。
```

### 6.15 @Autowired和@Resource注解的区别是什么？（5分）
```txt
1. @Autowired 是 Spring 注解；@Resource 是 JSR-250 规范。
2. @Autowired 默认按类型，多个 Bean 时配合 @Qualifier 或字段名。
3. @Resource 默认按名称，找不到再按类型。
4. @Autowired 支持 required=false；@Resource 可指定 name/type。
5. 更推荐构造器注入。
```

### 6.16 Spring Bean的单例模式为什么是线程安全的？（6分）
```txt
准确说 Spring 单例 Bean 不天然线程安全，是否安全取决于是否有共享可变状态。
1. singleton 只表示容器中一个实例。
2. Controller/Service 通常无状态，方法局部变量在线程栈中隔离，所以通常安全。
3. 若单例 Bean 有可变成员变量并被并发读写，就不安全。
4. 解决：避免可变成员、用局部变量、线程安全容器、加锁、ThreadLocal 后及时 remove，或改作用域。
```

### 6.17 什么是Spring的循环依赖问题？如何解决？（8分）
```txt
循环依赖是 Bean 之间互相依赖，如 A 依赖 B，B 依赖 A。
1. Spring 可解决单例 setter/字段注入循环依赖，核心是三级缓存。
2. 一级 singletonObjects：完整 Bean。
3. 二级 earlySingletonObjects：早期 Bean。
4. 三级 singletonFactories：ObjectFactory，用于提前暴露代理对象。
5. 构造器循环依赖和 prototype 循环依赖无法解决。
6. 解决：重构依赖、抽中间服务、事件发布、@Lazy、setter 注入，但不建议设计上依赖循环。
```

---

## 7 RocketMQ阶段考卷

### 7.1 RocketMQ的作用是什么？（5分）
```txt
1. 异步解耦：生产者和消费者解耦。
2. 削峰填谷：高峰流量进入 MQ 缓冲。
3. 顺序消息：保证同一业务键顺序。
4. 事务消息：保证本地事务和消息发送最终一致。
5. 延迟消息：订单超时关闭、延迟通知。
6. 支持广播/集群消费等模型。
```

### 7.2 Broker有哪些核心功能？（5分）
```txt
1. 接收并持久化 Producer 消息。
2. 为 Consumer 提供拉取消息能力。
3. 维护 Topic、Queue、消费进度、订阅关系。
4. 管理 CommitLog、ConsumeQueue、IndexFile。
5. 支持主从复制、刷盘、消息过滤、延迟消息、事务回查。
6. 向 NameServer 注册路由和心跳。
```

### 7.3 生产者是如何发送消息的？（5分）
```txt
1. Producer 从 NameServer 获取 Topic 路由。
2. 根据负载均衡选择 MessageQueue。
3. 发送消息到对应 Broker。
4. Broker 写 CommitLog，并按策略刷盘。
5. Broker 返回结果，Producer 根据结果成功、失败或重试。
6. 支持同步、异步、单向发送；可靠性高用同步。
```

### 7.4 RocketMQ消息是如何存储的？（5分）
```txt
1. 消息主体顺序写 CommitLog，所有 Topic 混合存储。
2. ConsumeQueue 保存 CommitLog 偏移量、消息大小、Tag Hash。
3. IndexFile 为消息 Key 建索引。
4. 写 CommitLog 后异步构建 ConsumeQueue 和 IndexFile。
5. 顺序写 + PageCache 提升吞吐。
6. 刷盘分同步刷盘和异步刷盘。
```

### 7.5 如何解决消息积压问题？（6分）
```txt
1. 先定位原因：消费者异常、消费慢、下游慢、队列少、突发流量。
2. 扩容 Consumer，但并发上限受 Queue 数限制。
3. 增加 Queue 并扩容消费者。
4. 优化消费逻辑：批量、异步、缓存、减少远程调用。
5. 对慢下游限流、熔断、降级。
6. 极端情况转储到新 Topic 用更多消费者处理。
7. 补充堆积量、消费耗时、失败率告警。
```

### 7.6 如何保证消息幂等？（7分）
```txt
RocketMQ 至少一次投递，业务必须幂等。
1. 使用业务唯一键，如订单号、流水号。
2. 消费前查幂等表/去重表。
3. 数据库唯一索引防重复。
4. 状态机控制状态流转。
5. Redis setnx 可做前置去重，关键业务仍要落库。
6. 业务处理成功后再返回消费成功。
7. 幂等记录和业务更新放同一本地事务。
```

### 7.7 如何保证消息不丢失？（6分）
```txt
1. 生产端同步发送，检查 SendResult，失败重试；关键消息落本地表补偿。
2. Broker 同步刷盘 SYNC_FLUSH。
3. 主从同步复制 SYNC_MASTER。
4. 消费端业务成功后再返回 CONSUME_SUCCESS。
5. 失败进入重试队列，多次失败进死信队列。
6. 监控发送失败、Broker、主从延迟、消费失败、死信、堆积。
```

### 7.8 RocketMQ事务消息是如何实现的？（6分）
```txt
1. Producer 先发 Half Message，消费者不可见。
2. Broker 持久化半消息后返回成功。
3. Producer 执行本地事务。
4. 根据本地事务结果 Commit 或 Rollback。
5. Commit 后消息可见，Rollback 后丢弃。
6. 未收到二次确认时 Broker 回查 Producer 本地事务状态。
7. 用于保证本地事务和消息发送最终一致。
```

### 7.9 RocketMQ延迟消息是如何实现的？（5分）
```txt
1. 发送时设置 delayTimeLevel 或定时时间。
2. Broker 先把延迟消息写入定时相关队列，不立即投递真实队列。
3. 定时任务扫描到期后投递回原 Topic 的 ConsumeQueue。
4. 4.x 默认固定延迟等级；5.x 定时/延迟能力更灵活。
5. 场景：订单超时关闭、延迟重试、支付结果检查。
```

### 7.10 RocketMQ如何保证消息顺序消费？（5分）
```txt
1. 生产端按业务键选择同一个 MessageQueue。
2. 单个 Queue 内消息天然有序。
3. 消费端使用 MessageListenerOrderly 顺序消费。
4. 集群中一个 Queue 同一时刻只分配给一个消费者实例。
5. 消费失败应暂停当前队列后续消费并重试。
6. 全局顺序需一个 Queue，吞吐低；常用局部顺序。
```

### 7.11 RocketMQ的集群架构是怎样的？（5分）
```txt
1. NameServer：注册中心，保存 Topic 路由，无状态多节点。
2. Broker：消息存储和投递，可分 Master/Slave。
3. Producer：获取路由后发送消息到 Broker。
4. Consumer：获取路由后从 Broker 拉取消息。
5. Broker 定期向 NameServer 注册路由并心跳。
6. 部署可选多 Master、多 Master 多 Slave、同步/异步复制。
```

### 7.12 广播消息和集群消息有什么区别？（5分）
```txt
1. 集群消费：同组内一条消息只被一个消费者实例消费，适合负载均衡。
2. 广播消费：同组内每个消费者实例都会消费同一条消息。
3. 集群消费进度由 Broker 管理；广播消费进度通常本地维护。
4. 集群消费重试和死信能力更完整；广播失败需业务处理。
5. 大多数业务使用集群消费。
```

### 7.13 RocketMQ怎么保证消息发到同一个Queue？（5分）
```txt
1. 使用 MessageQueueSelector。
2. 根据业务键选择队列，如 orderId.hashCode() % queueList.size()。
3. 同一业务键固定路由同一 Queue。
4. 同一业务键消息应由同一 Producer 顺序发送。
5. 队列数量变化会影响取模结果，扩容需谨慎。
```

### 7.14 RocketMQ是如何保证数据的高容错性的？（6分）
```txt
1. Broker 主从复制保存副本。
2. 同步刷盘可靠性高，异步刷盘性能好。
3. 同步复制主从都成功再返回，可靠性更高。
4. 多 Broker 分布 Topic，单点故障影响有限。
5. NameServer 多节点无状态。
6. Producer 故障规避和重试降低发送失败。
7. Consumer 重试、死信队列、offset 持久化提升消费容错。
```

### 7.15 在不开启容错的情况下，轮询队列进行发送，如果失败了，重试的时候过滤失败的Broker；如果开启了容错策略，会通过RocketMQ的预测机制来预测一个Broker是否可用……（完整描述容错发送策略）（7分）
```txt
1. Producer 默认轮询 MessageQueue 发送。
2. 未开启 sendLatencyFaultEnable 时，失败重试会尽量避开刚失败的 Broker，但不长期规避。
3. 开启后启用 LatencyFaultTolerance 延迟故障规避。
4. Producer 记录 Broker 发送耗时和失败情况，并映射为不可用时间窗口。
5. 选择队列时优先选可用 Broker，跳过预测不可用 Broker。
6. 如果都不可用，则选择相对最优 Broker 尝试。
7. 本质是基于历史延迟和失败做短期熔断，提升成功率和延迟稳定性。
```

### 7.16 任何一台Broker突然宕机了怎么办？（7分）
```txt
1. Producer 发送失败会重试并刷新路由；开启故障规避会临时避开该 Broker。
2. Consumer 拉取失败后重新负载均衡。
3. NameServer 心跳超时后移除故障 Broker 路由。
4. 有 Slave 时数据可从副本恢复；是否自动切主取决于部署模式和版本能力。
5. 异步刷盘/异步复制可能丢少量数据。
6. 业务用发送重试、本地消息表、消费幂等、死信补偿兜底。
7. 运维需检查主从延迟、CommitLog、消费堆积和路由状态。
```

### 7.17 如果让你来动手实现一个分布式消息中间件，整体架构你会如何设计实现？（需考虑快速扩容、持久化、高可用、数据0丢失、服务端/客户端简单等）（10分）
```txt
1. 架构：Registry/NameServer 负责路由，Broker 负责存储投递，Producer/Consumer SDK 负责发送拉取。
2. 存储：CommitLog 顺序写，Topic-Queue 建逻辑索引，PageCache + 批量刷盘提升吞吐。
3. 扩容：Topic 拆多个 Queue，Queue 分布到 Broker，新增 Broker 后迁移或新增队列。
4. 高可用：Broker 多副本、同步复制、选主、故障转移；注册中心无状态多节点。
5. 不丢：生产端同步发送 + 本地消息表；Broker 同步刷盘 + 同步复制；消费成功后提交 offset。
6. 顺序：同一业务键固定 Queue，消费端单 Queue 串行。
7. 幂等：业务唯一键、幂等表、状态机。
8. 延迟/事务：时间轮或定时队列；半消息 + 本地事务 + 回查。
9. 客户端：封装路由刷新、重试、负载均衡、序列化、批量、限流、监控。
10. 可观测：堆积、TPS、延迟、失败率、主从延迟、刷盘耗时、磁盘水位。
```