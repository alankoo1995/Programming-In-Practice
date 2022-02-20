# Programming-In-Practice

## Mysql
1. 避免使用Select * ... 在修改表格字段/索引时有可能导致metalock; 

** 实例：** 服务中存在大量的慢sql查询（select * )，此时希望通过增加某字段索引解决，但是因为每条Select * 会先查所有字段，从而使得加索引操作被阻塞。最后只能停掉服务中的查询接口，然后kill掉存量查询/或等它们消化完才能把索引加上

2. 如果某个字段存储数据量较大，频繁更新这个记录会导致binlog过大，从而造成主从同步时延

3. 主流程最好读写都用主库，避免出现主从同步延时时导致资损

4. gorm在事务中panic会导致事务一直不结束。如果上一个事务未结束又加了锁，那锁将一直不能释放

** 实例：** 在一个长事务中，未commit/rollback，程序panic，虽然服务后来自动recover，但事务没有结束，假如某表被加上X锁(排他锁)，insert/update/delete等操作将一直无法进行

排查方法：

`select * from information_schema.innodb_trx` 寻找LOCK WAIT状态SQL

`select * from information_schema.innodb_locks;`查看目前的锁和持有它的事务ID

`select * from information_schema.innodb_lock_waits` 查看是哪个事务在等待锁释放

## Memory
1. 当需要一个超大规模的数据load进内存时，应当考虑是否可以将这部分数据缓存在本地，因为并发场景下大量重复的数据可能导致内存飙升

## Redis
1. 主动释放redis分布式锁导致幂等性失效

** 实例： ** 下单接口依赖redis分布式锁保证接口幂等性，自己原有的锁过期后接口又主动释放了另一个请求加上的锁（key值一样），导致幂等性失效，在同一时间可能有多笔请求被执行。解决方案：释放锁时带上请求的特征值，每个请求只能释放自己加上的锁

## 分布式事务

## TCC

## 可靠消息确保最终一致性：延时队列
