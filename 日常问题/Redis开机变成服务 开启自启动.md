
1）进入redis文件夹，输入 redis-server.exe --service-install redis.windows.conf --loglevel verbose ，其中参数 -loglevel verbose 表示记录日志等级，如果没有redis.windows.conf文件就 redis-server.exe --service-install redis.conf --loglevel verbose

（2）鼠标右键「任务栏」–> 点击「任务管理器」–> 选择「服务」选项 –> 点击下方「打开服务」按钮，打开服务窗口之后就可以找到 Redis 的服务。初始状态是停止，右键选择属性可以更改启动类型