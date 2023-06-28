**nohup结合**

我们经常使用`nohup command &`命令形式来启动一些后台程序，比如一些java服务：

```
# nohup java -jar xxxx.jar &
```

为了不让一些执行信息输出到前台（控制台），我们还会加上刚才提到的`>/dev/null 2>&1`命令来丢弃所有的输出：

```
# nohup java -jar xxxx.jar >/dev/null 2>&1 &
```





```

curr_path=$(pwd);echo $curr_path;
pid=$(ps aux | grep 'flink-web' | grep -v grep | awk '{print $2}');echo $pid;
java -jar >/dev/null 2>&1 &

```

