# Redis

Redis是一个支持key-value形式的内存数据库。

key为字符串，value支持5种类型的数据结构:

一、Value数据类型

String
	最基本的结构。

List
	列表，若干个字符串组成的列表。

Set
	集合，value是若干个字符串组成的集合。

ZSet
	有序集合， ZSet是在set的基础上增加了排序的功能。

Hash
	value是一个hash表，由若干个key-value对组成。
	
	
二、应用场景

Redis 消息队列：


由于它自身的某种特性使得它可以用来做消息队列，它有几个阻塞式的API可以使用，正是这些阻塞式的API让其有能力做消息队列；另外，做消息队列的其他特性例如FIFO（先入先出）也很容易实现，只需要一个list对象从头取数据，从尾部塞数据即可；redis能做消息队列还得益于其list对象blpop brpop接口以及Pub/Sub（发布/订阅）的某些接口，它们都是阻塞版的，所以可以用来做消息队列。
[Redis实现简单的消息队列](https://www.cnblogs.com/timothy-lai/p/5711840.html)

场景：
	秒杀（资源争夺）并发解决方案  - 并行变串行
	https://blog.csdn.net/u011870280/article/details/80025387


三、Redis 搭建集群
