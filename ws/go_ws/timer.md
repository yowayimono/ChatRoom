<!--
 * @Author: yowayimono
 * @Date: 2023-06-12 13:50:48
 * @LastEditors: yowayimono
 * @LastEditTime: 2023-06-12 13:50:54
 * @Description: nothing
-->
这段代码的作用是创建一个定时任务，用于定期清理离线的WebSocket连接。

逐行分析如下：

1. 导入`cron`包，该包用于创建和执行定时任务。
2. 定义了一个名为`CleanOfflineConn`的函数，该函数用于创建定时任务并启动。
3. 创建一个新的`cron`实例，用于管理定时任务。
4. 定义了一个字符串变量`spec`，表示定时任务的执行时间规则。在这个例子中，规定为每天的每分钟执行一次，即`* * * * *`。
5. 使用`c.AddFunc(spec, func())`向定时任务中添加一个函数。该函数在定时任务触发时被执行。
6. 在函数中调用`HandelOfflineCoon()`函数，用于处理离线的WebSocket连接。这个函数的实现在代码中没有给出，可能在其他地方定义。
7. 使用`go c.Start()`启动定时任务，使其开始按照指定的时间规则执行。

总结：该代码片段用于创建一个定时任务，每分钟执行一次，用于处理离线的WebSocket连接。定时任务的触发会调用`HandelOfflineCoon()`函数。