# 缓存

## LRU

LRU是Least Recently Used的缩写，即最近最少使用，常用于页面置换算法，是为虚拟页式存储管理服务的。

好多种

- LRU-K
- Multi Queue（MQ）
- Two queues（2Q）
- LRU

## 典型实现

- memcache
- redis

## redis

5种数据类型

- String——字符串
- Hash——字典
- List——列表（链表（redis 使用双端链表实现的 List））
- Set——集合（一堆不重复值的组合，交并补）
- Sorted Set——有序集合（增加了一个权重参数 score，可排序）

推荐https://github.com/luin/ioredis


典型应用

- 自增id
- lru缓存