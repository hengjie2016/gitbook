# 4.1 新增节点

```
create [-s] [-e] path data  # 其中-s 为有序节点，-e 临时节点
```


创建持久化节点并写入数据：

```
create /hadoop "123456"

```

创建持久化有序节点，此时创建的节点名为指定节点名 + 自增序号

```
[zk: localhost:2181(CONNECTED) 0] create -s /a "aaa"
Created /a0000000000
[zk: localhost:2181(CONNECTED) 1] create -s /b "bbb"
Created /b0000000001
[zk: localhost:2181(CONNECTED) 2] create -s /c "ccc"
Created /c0000000002
```


创建临时节点，临时会在会话过期后被删除：

```
[zk: localhost:2181(CONNECTED) 3] create -e /tmp "tmp"

```

创建临时有序节点，临时会在会话过期后被删除：

```
[zk: localhost:2181(CONNECTED) 4] create -s -e /aa 'aaa'
Created /aa0000000004
[zk: localhost:2181(CONNECTED) 5] create -s -e /bb 'bbb'
Created /bb0000000005
[zk: localhost:2181(CONNECTED) 6] create -s -e /cc 'ccc'
Created /cc0000000006
```



# 4.2 更新节点

更新节点的命令是 set，可以直接进行修改，如下：

```
[zk: localhost:2181(CONNECTED) 9] set /hadoop "345"     
cZxid = 0xa
ctime = Tue Jun 02 09:53:13 CST 2020
mZxid = 0xb
mtime = Tue Jun 02 09:53:20 CST 2020
pZxid = 0xa
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 3
numChildren = 0
```


也可以基于版本号进行修改，此时类似于乐观锁机制，当你传入的数据版本号(dataVersion)和当前节点的数据版本号不符合时，zookeeper会拒绝本次修改：

```
[zk: localhost:2181(CONNECTED) 12] set /hadoop "159" 2
version No is not valid : /hadoop
```



# 4.3 删除节点

删除节点的语法如下：

```
delete path [version]
和更新节点数据一样，也可以传入版本号，当你传入的数据版本号(dataVersion)和当前节点的数据版本号不符合时，zookeeper会拒绝本次删除操作：
```

```
[zk: localhost:2181(CONNECTED) 13] delete /hadoop 0
version No is not valid : /hadoop
[zk: localhost:2181(CONNECTED) 14] delete /hadoop 1
[zk: localhost:2181(CONNECTED) 15]
```

要想删除某个节点及其所有后代节点，可以使用递归删除，命令为 rmr path。

# 4.4 查看节点

```
[zk: localhost:2181(CONNECTED) 17] get /hadoop
123456
cZxid = 0x11
ctime = Tue Jun 02 09:59:49 CST 2020
mZxid = 0x11
mtime = Tue Jun 02 09:59:49 CST 2020
pZxid = 0x11
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 6
numChildren = 0
```

节点各个属性如下表。其中一个重要的概念是 Zxid(ZooKeeper Transaction Id)，ZooKeeper 节点的每一次更改都具有唯一的 Zxid，如果 Zxid1 小于 Zxid2，则
Zxid1 的更改发生在 Zxid2 更改之前。

状态属性	说明
cZxid	数据节点创建时的事务 ID
ctime	数据节点创建时的时间
mZxid	数据节点最后一次更新时的事务 ID
mtime	数据节点最后一次更新时的时间
pZxid	数据节点的子节点最后一次被修改时的事务 ID
cversion	子节点的更改次数
dataVersion	节点数据的更改次数
aclVersion	节点的 ACL 的更改次数
ephemeralOwner	如果节点是临时节点，则表示创建该节点的会话的SessionID；如果节点是持久节点，则该属性值为 0
dataLength	数据内容的长度
numChildren	数据节点当前的子节点个数



属性说明：

cZxid：数据节点创建时的事务ID
ctime：数据节点创建时的时间
mZxid：数据节点最后一次更新时的事务ID
mtime：数据节点最后一次更新时的时间
pZxid：数据节点的子节点最后一次被修改的事务ID
cversion：子节点的更改次数
dataVersion：节点数据的更改次数
aclVersion：节点的ACL的更改次数
ephemer alowner：如果节点是临时节点，则表示创建该节点的会话的SessionID；如果节点是持久节点，则该属性为0
dataLength：数据内容的长度
numChildren：数据节点当前的子节点个数
节点类型
zookeeper中的节点有两种，分别为临时节点和永久节点。节点的类型在创建时即被确定，并且不能改受。
临时节点：该节点的生命周期依赖于创建它们的会话。一旦会话(Session)结束，临时节点将被自动删除，当然可以也可以手动删除。虽然每个临时的Znode都会绑走到一个客户端会话，但他们对所有的客户端还是可见的。另外，ZooKeeper的临时节点不允许拥有子节点。
持久化节点：该节点的生命周期不依赖于会话，并且只有在客户端显示执行删除操作的时候，他们才能被删除

# 4.5 查看节点状态

可以使用 stat命令查看节点状态，它的返回值和 get命令类似，但不会返回节点数据

```
[zk: localhost:2181(CONNECTED) 9] stat /hadoop
cZxid = 0x18
ctime = Tue Jun 02 10:43:30 CST 2020
mZxid = 0x18
mtime = Tue Jun 02 10:43:30 CST 2020
pZxid = 0x18
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 6
numChildren = 0
```



# 4.6 查看节点列表

查看节点列表有 ls path 和 ls2 path 两个命令，后者是前者的增强，不仅可以查看指定路径下的所有节点，还可以查看当前节点的信息

```
[zk: localhost:2181(CONNECTED) 13] ls /hadoop
[node2, node1]
[zk: localhost:2181(CONNECTED) 14] ls2 /hadoop
[node2, node1]
cZxid = 0x18
ctime = Tue Jun 02 10:43:30 CST 2020
mZxid = 0x18
mtime = Tue Jun 02 10:43:30 CST 2020
pZxid = 0x1a
cversion = 2
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 6
numChildren = 2
```



# 4.7 监听器get path [watch]

  使用 get path [watch] 注册的监听器能够在节点内容发生改变的时候，向客户端发出通知。需要注意的是 zookeeper 的触发器是一次性的 (One-time trigger)，即触发一次后就会立即失效。

```
[zk: localhost:2181(CONNECTED) 16] get /hadoop watch
[zk: localhost:2181(CONNECTED) 17] set /hadoop "456"
WATCHER::
WatchedEvent state:SyncConnected type:NodeDataChanged path:/hadoop
```



# 4.8 监听器stat path [watch]

  使用 stat path [watch] 注册的监听器能够在节点状态发生改变的时候，向客户端发出通知

```
[zk: localhost:2181(CONNECTED) 18] stat /hadoop watch
[zk: localhost:2181(CONNECTED) 19] set /hadoop 1122  
WATCHER::cZxid = 0x18
WatchedEvent state:SyncConnected type:NodeDataChanged path:/hadoop
```



# 4.9 监听器ls\ls2 path [watch]

  使用 ls path [watch] 或 ls2 path [watch] 注册的监听器能够监听该节点下所有子节点的增加和删除操作。

```
[zk: localhost:2181(CONNECTED) 20] ls /hadoop watch
[node2, node1]
[zk: localhost:2181(CONNECTED) 21] create /hadoop/yran "aaa"
WATCHER::Created /hadoop/yran
WatchedEvent state:SyncConnected type:NodeChildrenChanged path:/hadoop
```

