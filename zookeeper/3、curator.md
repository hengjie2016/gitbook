maven

```

<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.7</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>2.6.0</version>
        <type>jar</type>
        <exclusions>
            <exclusion>
                <groupId>org.apache.zookeeper</groupId>
                <artifactId>zookeeper</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.apache.zookeeper</groupId>
        <artifactId>zookeeper</artifactId>
        <version>3.4.10</version>
        <type>jar</type>
    </dependency>
    <dependency>
        <groupId>org.apache.curator</groupId>
        <artifactId>curator-recipes</artifactId>
        <version>2.6.0</version>
        <type>jar</type>
    </dependency>
</dependencies>


```





连接

```
package com.xue.curator;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;

public class CuratorConnection {

    public static final String IP = "server1:2181,server2:2181,server3:2181";

    public static void main(String[] args) {

        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        CuratorFramework client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(5000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
        System.out.println(client.getState());
        client.close();

    }

}

```

## 查看节点

```
package com.xue.curator;


import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.api.BackgroundCallback;
import org.apache.curator.framework.api.CuratorEvent;
import org.apache.curator.retry.ExponentialBackoffRetry;
import org.apache.zookeeper.data.Stat;


public class CuratorGet {

    public static final String IP = "server1:2181,server2:2181,server3:2181";

    private static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(10000).retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void after() {
        client.close();
    }

    public static void main(String[] args) throws Exception {
        before();
        get2();
        after();
    }

    public static void get1() throws Exception {
        // 读取节点数据
        byte[] bys = client.getData()
                // 节点的路径
                .forPath("/create/node11");
        System.out.println(new String(bys));
    }

    public static void get2() throws Exception {
        // 读取数据时读取节点的属性
        Stat stat = new Stat();
        byte[] bys = client.getData()
                // 读取属性
                .storingStatIn(stat)
                .forPath("/create/node11");
        System.out.println(new String(bys));
        System.out.println(stat.toString());
    }

    public static void get3() throws Exception {
        // 异步方式读取节点的数据
        client.getData()
                .inBackground((curatorFramework, curatorEvent) -> {
                    // 节点的路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    // 数据
                    System.out.println(new String(curatorEvent.getData()));
                })
                .forPath("/node1");
        Thread.sleep(5000);
        System.out.println("结束");
    }
}


```

## 查看子节点

```
package com.xue.curator;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;

import java.util.List;

public class CuratorGetChild {
    public static final String IP = "server1:2181,server2:2181,server3:2181";
    static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(10000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void main(String[] args) throws Exception {
        before();
        getChild1();
        after();
    }

    public static void after() {
        client.close();
    }

    public static void getChild1() throws Exception {
        // 读取子节点数据
        List<String> list = client.getChildren()
                // 节点路径
                .forPath("/create");
        for (String str : list) {
            System.out.println(str);
        }
    }

    public static void getChild2() throws Exception {
        // 异步方式读取子节点数据
        client.getChildren()
                .inBackground((curatorFramework, curatorEvent) -> {
                    // 节点路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    // 读取子节点数据
                    List<String> list = curatorEvent.getChildren();
                    for (String str : list) {
                        System.out.println(str);
                    }
                })
                .forPath("/get");
        Thread.sleep(3000);
        System.out.println("结束");
    }

}

```

## 新增

```
package com.xue.curator;


import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.api.BackgroundCallback;
import org.apache.curator.framework.api.CuratorEvent;
import org.apache.curator.retry.ExponentialBackoffRetry;


public class CuratorDelete {

    public static final String IP = "server1:2181,server2:2181,server3:2181";

    private static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(10000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void after() {
        client.close();
    }

    public static void main(String[] args) throws Exception {
        before();
        delete4();
        after();
    }
    public static void delete1() throws Exception {
        // 删除节点
        client.delete()
                // 节点的路径
                .forPath("/create/node1");
        System.out.println("结束");
    }


    public static void delete2() throws Exception {
        client.delete()
                // 版本号
                .withVersion(0)
                .forPath("/create/node1");
        System.out.println("结束");
    }

    public static void delete3() throws Exception {
        //删除包含字节点的节点
        client.delete()
                .deletingChildrenIfNeeded()
                .withVersion(-1)
                .forPath("/node1");
        System.out.println("结束");
    }

    public static void delete4() throws Exception {
        // 异步方式删除节点
        client.delete()
                .deletingChildrenIfNeeded()
                .withVersion(-1)
                .inBackground(new BackgroundCallback() {
                    @Override
                    public void processResult(CuratorFramework curatorFramework, CuratorEvent curatorEvent) throws Exception {
                        // 节点路径
                        System.out.println(curatorEvent.getPath());
                        // 事件类型
                        System.out.println(curatorEvent.getType());
                        System.out.println(curatorEvent.getName());
                    }
                })
                .forPath("/create/node3");
        Thread.sleep(3000);
        System.out.println("结束");
    }
}


```



## 更新

```
package com.xue.curator;


import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;

import java.util.Arrays;

public class CuratorSet {

    public static final String IP = "server1:2181,server2:2181,server3:2181";

    static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(5000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void after() {
        client.close();
    }

    public static void main(String[] args) throws Exception {
        before();
        set3();
        after();
    }

    public static void set1() throws Exception {
        // 更新节点
        client.setData()
                // arg1:节点的路径
                // arg2:节点的数据
                .forPath("/create/node1", "node113335".getBytes());
        System.out.println("结束");
    }

    public static void set2() throws Exception {
        client.setData()
                // 指定版本号
                .withVersion(16)
                .forPath("/create/node1", "node1111".getBytes());
        System.out.println("结束");
    }

    public static void set3() throws Exception {
        // 异步方式修改节点数据
        client.setData()
                .withVersion(-1)
                .inBackground((curatorFramework, curatorEvent) -> {
                    // 节点的路径
                    System.out.println(curatorEvent.getPath());
                    // 事件的类型
                    System.out.println(curatorEvent.getType());
                    System.out.println(Arrays.toString(curatorEvent.getData()));
                }).forPath("/create/node1", "node1fwgrgew".getBytes());
        Thread.sleep(3000);
        System.out.println("结束");
    }
}



```

## 删除节点

```
package com.xue.curator;

import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.api.BackgroundCallback;
import org.apache.curator.framework.api.CuratorEvent;
import org.apache.curator.retry.ExponentialBackoffRetry;


public class CuratorDelete {

    public static final String IP = "server1:2181,server2:2181,server3:2181";
    private static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(10000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void after() {
        client.close();
    }

    public static void main(String[] args) throws Exception {
        before();
        delete4();
        after();
    }

    public static void delete1() throws Exception {
        // 删除节点
        client.delete()
                // 节点的路径
                .forPath("/create/node1");
        System.out.println("结束");
    }


    public static void delete2() throws Exception {
        client.delete()
                // 版本号
                .withVersion(0)
                .forPath("/create/node1");
        System.out.println("结束");
    }

    public static void delete3() throws Exception {
        //删除包含字节点的节点
        client.delete()
                .deletingChildrenIfNeeded()
                .withVersion(-1)
                .forPath("/node1");
        System.out.println("结束");
    }

    public static void delete4() throws Exception {
        // 异步方式删除节点
        client.delete()
                .deletingChildrenIfNeeded()
                .withVersion(-1)
                .inBackground((curatorFramework, curatorEvent) -> {
                    // 节点路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    System.out.println(curatorEvent.getName());
                })
                .forPath("/create/node3");
        Thread.sleep(3000);
        System.out.println("结束");
    }
    
}


```

## 检查节点是否存在

```
package com.xue.curator;


import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;
import org.apache.zookeeper.data.Stat;


public class CuratorExists {
    public static final String IP = "server1:2181,server2:2181,server3:2181";
    private static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(10000).retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void after() {
        client.close();
    }

    public static void main(String[] args) throws Exception {
        before();
        exists1();
        after();
    }


    public  static void exists1() throws Exception {
        // 判断节点是否存在
        Stat stat= client.checkExists()
                // 节点路径
                .forPath("/node1");
        System.out.println(stat.getVersion());
    }

    public  static void exists2() throws Exception {
        // 异步方式判断节点是否存在
        client.checkExists()
                .inBackground((curatorFramework, curatorEvent) -> {
                    // 节点路径
                    System.out.println(curatorEvent.getPath());
                    // 事件类型
                    System.out.println(curatorEvent.getType());
                    System.out.println(curatorEvent.getStat().getVersion());
                })
                .forPath("/node1");
        Thread.sleep(5000);
        System.out.println("结束");
    }

}

```

## watcherAPI

 curator提供了两种Watcher(Cache)来监听结点的变化

Node Cache : 只是监听某一个特定的节点，监听节点的新增和修改
PathChildren Cache : 监控一个ZNode的子节点. 当一个子节点增加， 更新，删除时， Path Cache会改变它的状态， 会包含最新的子节点， 子节点的数据和状态

```
package com.xue.curator;


import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.framework.recipes.cache.NodeCache;
import org.apache.curator.framework.recipes.cache.PathChildrenCache;
import org.apache.curator.retry.ExponentialBackoffRetry;

import java.util.Arrays;

public class CuratorWatcher {
    public static final String IP = "server1:2181,server2:2181,server3:2181";
    static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(10000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void main(String[] args) throws Exception {
        before();
        watcher2();
        after();
    }

    public static void after() {
        client.close();
    }

    public static void watcher1() throws Exception {
        // 监视某个节点的数据变化
        // arg1:连接对象
        // arg2:监视的节点路径
        final NodeCache nodeCache = new NodeCache(client, "/create");
        // 启动监视器对象
        nodeCache.start();
        // 节点变化时回调的方法
        nodeCache.getListenable().addListener(() -> {
            System.out.println(nodeCache.getCurrentData().getPath());
            System.out.println(Arrays.toString(nodeCache.getCurrentData().getData()));
        });
        Thread.sleep(100000);
        System.out.println("结束");
        //关闭监视器对象
        nodeCache.close();
    }

    public static void watcher2() throws Exception {
        // 监视子节点的变化
        // arg1:连接对象
        // arg2:监视的节点路径
        // arg3:事件中是否可以获取节点的数据
        PathChildrenCache pathChildrenCache = new PathChildrenCache(client, "/create", true);
        // 启动监听
        pathChildrenCache.start();
        // 当子节点方法变化时回调的方法
        pathChildrenCache.getListenable()
                .addListener((curatorFramework, pathChildrenCacheEvent) -> {
                    // 节点的事件类型
                    System.out.println(pathChildrenCacheEvent.getType());
                    // 节点的路径
                    System.out.println(pathChildrenCacheEvent.getData().getPath());
                    // 节点数据
                    System.out.println(Arrays.toString(pathChildrenCacheEvent.getData().getData()));
                });
        Thread.sleep(100000);
        System.out.println("结束");
        // 关闭监听
        pathChildrenCache.close();

    }
}


```

## 事务

```
package com.xue.curator;


import org.apache.curator.RetryPolicy;
import org.apache.curator.framework.CuratorFramework;
import org.apache.curator.framework.CuratorFrameworkFactory;
import org.apache.curator.retry.ExponentialBackoffRetry;

public class CuratorTransaction {

    public static final String IP = "server1:2181,server2:2181,server3:2181";

    static CuratorFramework client;

    public static void before() {
        RetryPolicy retryPolicy = new ExponentialBackoffRetry(1000, 3);
        client = CuratorFrameworkFactory.builder()
                .connectString(IP)
                .sessionTimeoutMs(5000)
                .retryPolicy(retryPolicy)
                .build();
        client.start();
    }

    public static void after() {
        client.close();
    }

    public static void main(String[] args) throws Exception {
        before();
        tra1();
        after();
    }

    public static void tra1() throws Exception {
        client.inTransaction()
                .create().forPath("/node1", "node1".getBytes())
                .and()
                .setData().forPath("/node2", "node2".getBytes())
                .and()
                .commit();
    }
}


```

