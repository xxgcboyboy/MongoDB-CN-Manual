# 查看集群配置View Cluster Configuration

## List Databases with Sharding Enabled 列出开启分片的数据库

To list the databases that have sharding enabled, query the databases collection in the [Config Database](https://docs.mongodb.com/manual/reference/config-database/#std-label-config-database). 

查询配置数据库中的`databases`集合，可以列出已开启分片功能的数据库列表。

A database has sharding enabled if the value of the partitioned field is true. 

如果一个数据库中`partitioned`字段的值为`true`，则该数据库已开启分片功能。

Connect to a [mongos](https://docs.mongodb.com/manual/reference/program/mongos/#mongodb-binary-bin.mongos) instance with a [mongo](https://docs.mongodb.com/manual/reference/program/mongo/#mongodb-binary-bin.mongo) shell, and run the following operation to get a full list of databases with sharding enabled:

使用mongo shell连接到一个mongos实例，运行以下命令获取一个完整的已开启分片的数据库列表：

```
use config
db.databases.find( { "partitioned": true } )
```

**EXAMPLE 示例**

You can use the following sequence of commands to return a list of all databases in the cluster:

可以使用如下命令返回集群中的所有数据库列表：

```
use config
db.databases.find()
```

If this returns the following result set:

如果上述命令返回如下结果集：

```
{ "_id" : "test", "primary" : "shardB", "partitioned" : false }
{ "_id" : "animals", "primary" : "shardA", "partitioned" : true }
{ "_id" : "farms", "primary" : "shardA", "partitioned" : false }
```

Then sharding is only enabled for the animals database.

那么只有`animals`数据库是已分片的。

## List Shards 列出分片

To list the current set of configured shards, use the [listShards](https://docs.mongodb.com/manual/reference/command/listShards/#mongodb-dbcommand-dbcmd.listShards) command, as follows:

使用[listShards](https://docs.mongodb.com/manual/reference/command/listShards/#mongodb-dbcommand-dbcmd.listShards) 命令，列出当前已配置的分片：

```
db.adminCommand( { listShards : 1 } )
```

## View Cluster Details 查看集群详情

To view cluster details, issue [db.printShardingStatus()](https://docs.mongodb.com/manual/reference/method/db.printShardingStatus/#mongodb-method-db.printShardingStatus) or [sh.status()](https://docs.mongodb.com/manual/reference/method/sh.status/#mongodb-method-sh.status). Both methods return the same output.

使用[db.printShardingStatus()](https://docs.mongodb.com/manual/reference/method/db.printShardingStatus/#mongodb-method-db.printShardingStatus)或者 [sh.status()](https://docs.mongodb.com/manual/reference/method/sh.status/#mongodb-method-sh.status)可以查看集群的详情。这两个命令会返回同样的结果。

**EXAMPLE**

**示例**

**In the following example output from sh.status()**

**以下示例的输出结果来自sh.status()**

- sharding version displays the version number of the shard metadata.
- `sharding version`显示了分片元数据的版本号
- shards displays a list of the [mongod](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod) instances used as shards in the cluster.
- `shards`显示了作为集群分片的[mongod](https://docs.mongodb.com/manual/reference/program/mongod/#mongodb-binary-bin.mongod)实例列表。
- databases displays all databases in the cluster, including database that do not have sharding enabled.
- `databases`显示了集群中的所有数据库，包括未开启分片的数据库。
- The chunks information for the foo database displays how many chunks are on each shard and displays the range of each chunk.
- `foo`数据库的`chunks`信息，显示了每个分片上有多少个数据块，以及每个数据块的范围。

```
--- Sharding Status ---
  sharding version: {
    "_id" : 1,
    "minCompatibleVersion" : 5,
    "currentVersion" : 6,
    "clusterId" : ObjectId("59a4443c3d38cd8a0b40316d")
  }
  shards:
    {  "_id" : "shard0000",  "host" : "m0.example.net:27018" }
    {  "_id" : "shard0001",  "host" : "m3.example2.net:27018" }
    {  "_id" : "shard0002",  "host" : "m2.example.net:27018" }
  active mongoses:
    "3.4.7" : 1
  autosplit:
    Currently enabled: yes
   balancer:
    Currently enabled:  yes
    Currently running:  no
    Failed balancer rounds in last 5 attempts:  0
    Migration Results for the last 24 hours:
       1 : Success
  databases:
    {  "_id" : "foo",  "partitioned" : true,  "primary" : "shard0000" }
        foo.contacts
            shard key: { "zip" : 1 }
            unique: false
            balancing: true
            chunks:
                shard0001    2
                shard0002    3
                shard0000    2
            { "zip" : { "$minKey" : 1 } } -->> { "zip" : "56000" } on : shard0001 { "t" : 2, "i" : 0 }
            { "zip" : 56000 } -->> { "zip" : "56800" } on : shard0002 { "t" : 3, "i" : 4 }
            { "zip" : 56800 } -->> { "zip" : "57088" } on : shard0002 { "t" : 4, "i" : 2 }
            { "zip" : 57088 } -->> { "zip" : "57500" } on : shard0002 { "t" : 4, "i" : 3 }
            { "zip" : 57500 } -->> { "zip" : "58140" } on : shard0001 { "t" : 4, "i" : 0 }
            { "zip" : 58140 } -->> { "zip" : "59000" } on : shard0000 { "t" : 4, "i" : 1 }
            { "zip" : 59000 } -->> { "zip" : { "$maxKey" : 1 } } on : shard0000 { "t" : 3, "i" : 3 }
    {  "_id" : "test",  "partitioned" : false,  "primary" : "shard0000" }
```



原文链接：https://docs.mongodb.com/manual/tutorial/view-sharded-cluster-configuration/

译者：张冲
