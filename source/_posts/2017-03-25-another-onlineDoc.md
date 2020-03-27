---
layout: post
title:  "es 集群查看"
date:   2020-03-27 01:30:13 +0800
categories: elasticsearch
tags: doc
comments: 1
---

es集群查看常用命令

### 命令
es一般使用GET方法，所以可以通过浏览器或者crul命令查看
```
curl -X get [请求的链接]
```

#### 查看节点状态
http://[主机IP]:[ES端口]
```
{
  "name" : "node-187",   # 节点名称
  "cluster_name" : "es",   # 集群名称
  "cluster_uuid" : "wn8n4hBTT-2YIGNf--AWVA",
  "version" : {
    "number" : "6.8.2",  # es版本
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "b506955",
    "build_date" : "2019-07-24T15:24:41.545295Z",
    "build_snapshot" : false,
    "lucene_version" : "7.7.0",  # lucene版本
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

#### 查看集群状态

http://ip:port/_cat/health?v
* _cat表示查看信息
* health表明返回的信息为集群健康信息
* ?v表示返回的信息加上头信息

```
epoch      timestamp cluster status node.total node.data shards pri relo init unassign pending_tasks max_task_wait_time active_shards_percent
1584582653 01:50:53  es      yellow          3         3    104  45    0    0       32             0                  -                 76.5%

集群的状态（status）：
        red红表示集群不可用，有故障。
        yellow黄表示集群不可靠但可用，一般单节点时就是此状态。
        green正常状态，表示集群一切正常。
        
节点数（node.total）：节点数，这里是3，表示该集群有三个节点。

数据节点数（node.data）：存储数据的节点数，这里是3。数据节点在Elasticsearch概念介绍有。

分片数（shards）：这是104，表示我们把数据分成多少块存储。

主分片数（pri）：primary shards，这里是45，实际上是分片数的两倍，因为有一个副本，如果有两个副本，这里的数量应该是分片数的三倍，这个会跟后面的索引分片数对应起来，这里只是个总数。

激活的分片百分比（active_shards_percent）：这里可以理解为加载的数据分片数，只有加载所有的分片数，集群才算正常启动，在启动的过程中，如果我们不断刷新这个页面，我们会发现这个百分比会不断加大。
```

#### 查看集群索引数

http://ip:port/_cat/indices?v

```
health status index                     uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   searchguard               u6B0gJl_T5esP_DHv7SwQA   1   2          6            1     86.6kb         34.2kb

索引健康（health）:
        green为正常
        yellow表示索引不可靠（单节点）
        red索引不可用。与集群健康状态一致。
状态（status），表明索引是否打开。
索引名称（index），这里有searchguard  。
uuid，索引内部随机分配的名称，表示唯一标识这个索引。
主分片（pri），.kibana为1，school为5，加起来主分片数为6，这个就是集群的主分片数。
文档数（docs.count），school在之前的演示添加了两条记录，所以这里的文档数为2。
已删除文档数（docs.deleted），这里统计了被删除文档的数量。
索引存储的总容量（store.size），这里school索引的总容量为6.4kb，是主分片总容量的两倍，因为存在一个副本。
主分片的总容量（pri.store.size），这里school的主分片容量是7kb，是索引总容量的一半。
```

#### 查看集群所在磁盘的分配状况(可查看集群地址)

http://ip:port/_cat/allocation?v

```
shards disk.indices disk.used disk.avail disk.total disk.percent host          ip            node
    39        9.1mb    13.9gb    133.8gb    147.7gb            9 10.111.24.188 10.111.24.188 node-188

分片数（shards），集群中各节点的分片数相同，都是6，总数为12，所以集群的总分片数为12。
索引所占空间（disk.indices），该节点中所有索引在该磁盘所点的空间。
磁盘使用容量（disk.used），已经使用空间41.6gb磁盘
可用容量（disk.avail），可用空间4.3gb
磁盘总容量（disk.total），总共容量45.9gb
磁盘便用率（disk.percent），磁盘使用率90%。
```

#### 查看集群节点

http://ip:port/_cat/nodes?v

```
ip            heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
10.111.24.189           54          96  12    0.67    0.86     0.99 mdi       *      node-189
10.111.24.188           30          97  11    1.29    1.19     1.04 mdi       -      node-188

master列，带*星号表明该节点是主节点。带-表明该节点是从节点。
heap.percent堆内存使用情况
ram.percent运行内存使用情况，cpu使用情况。
```

#### 查看集群的其他信息

http://ip:port/_cat/

```
=^.^=
/_cat/allocation
/_cat/shards
/_cat/shards/{index}
/_cat/master
/_cat/nodes
/_cat/tasks
/_cat/indices
/_cat/indices/{index}
/_cat/segments
/_cat/segments/{index}
/_cat/count
/_cat/count/{index}
/_cat/recovery
/_cat/recovery/{index}
/_cat/health
/_cat/pending_tasks
/_cat/aliases
/_cat/aliases/{alias}
/_cat/thread_pool
/_cat/thread_pool/{thread_pools}
/_cat/plugins
/_cat/fielddata
/_cat/fielddata/{fields}
/_cat/nodeattrs
/_cat/repositories
/_cat/snapshots/{repository}
/_cat/templates
```



