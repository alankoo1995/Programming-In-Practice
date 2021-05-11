# Programming-In-Practice

## Mysql
1. 避免使用Select * ... 在修改表格字段/索引时有可能导致metalock; 
**实例：**服务中存在大量的慢sql查询（select * )，此时希望通过增加某字段索引解决，但是因为每条Select * 会先查所有字段，从而使得加索引操作被阻塞。最后只能停掉服务中的查询接口，然后kill掉存量查询/或等它们消化完才能把索引加上
