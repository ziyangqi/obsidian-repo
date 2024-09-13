


### 查看端口

```shell
# 查看端口
netstat -ano

# 查看特定的端口 查看80的端口
netstat -ano | findstr :80

#   想查看端口对应的服务名称，可以使用以下命令
netstat -ano | findstr :80 | findstr LISTENING

# 了进一步了解监听特定端口的服务，你可以使用以下命令与进程ID（PID）关联
netstat -ano | findstr :80 | findstr LISTENING | find /i "PID"

#然后，你可以使用任务管理器或`tasklist`命令来查看这个PID对应的进程详细信息：  
tasklist /fi "PID eq <PORT_PID>"
```

### 查看这个端口是那个应用使用的

```bash
tasklist | findstr [PID]
```