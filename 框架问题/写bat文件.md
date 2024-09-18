
```bash
@echo off
REM 打开 Redis 文件夹并启动 Redis 服务器
cd /d "D:\Program Files\javapz\redis5"
start "" "redis-server.exe" "redis.windows.conf"

REM 执行第二个命令，启动 Nacos
echo 执行第二个命令
cd /d "D:\Program Files\javapz\nacos\bin"
start "" "startup.cmd" -m standalone


```

- REM是备注
- cd /d 进入这个文件夹
- start 是执行这个命令