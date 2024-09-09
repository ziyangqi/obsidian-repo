
### 参考连接
https://blog.csdn.net/WQearl/article/details/136835160


#### 1 打包
命令 : npm run build  或者 yarn bulid 
或者查看自己的Package.json 中会否有build内容
打包后完全的内容会放在dist的文件夹中
*dist就是完全的文件夹*


#### 2 测试连接：

- 安装serve 安装命令npm i serve -g
- 运行测试命令 : serve dist 运行查看是否打包成功


#### 3 下载nginx
 - 地址：[下载nginx地址](http://nginx.org/en/download.html "http://nginx.org/en/download.html")
 - 将打包好的nginx放到nginx的html文件夹中
 - 找到config文件中的nginx.conf文件，并修改。
``` config
# 全局块
worker_processes  1;
 
#event块
events {
    worker_connections  1024;
}
 
#http块
http {
 
    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;
 
    #server 块 (可以有多个，对应不同的服务)
    server {
        #前端访问端口
        listen 5000;
 
        gzip on;
        gzip_min_length 1k;
        gzip_comp_level 9;
        gzip_types text/plain application/javascript application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
        gzip_vary on;
        gzip_disable "MSIE [1-6]\.";
 
        #location块
        location / {
            root html/dist;
            index index.html index.htm;
            try_files $uri $uri/ /index.html;
        }
 
        # 配置自己的代理
        #  /aidss/api/system/：配置需要代理接口
        #proxy_pass：配置需要寻找的目标服务器
 
        location /aidss/api/system/ {
 	        proxy_pass http://192.168.1.56:8080;
            proxy_set_header   X-Forwarded-Proto $scheme;
            proxy_set_header   Host              $http_host;
            proxy_set_header   X-Real-IP         $remote_addr;
        }
       
    }
 
}
```

-  全局块**worker_processes 1;**：指定Nginx使用的工作进程数。通常设为与CPU核心数相同或更高，以提高并发处理能力。这里设为1，表示只使用一个工作进程
-  事件块**worker_connections 1024;**：指定每个工作进程可以同时打开的最大连接数。这里设为1024，表示每个进程最多可以处理1024个连接。
```
### HTTP块 (`http { ... }`)

这个块包含了HTTP服务的配置，影响到所有的HTTP请求处理。

- **include mime.types;**：引入`mime.types`文件，定义不同文件扩展名对应的MIME类型，便于Nginx正确处理不同类型的文件。
    
- **default_type application/octet-stream;**：设置默认的MIME类型为`application/octet-stream`，当文件类型未被识别时使用此类型。
    
- **sendfile on;**：启用高效文件传输模式（sendfile），以提高传输效率。
    
- **keepalive_timeout 65;**：设置保持连接的超时时间，65秒后未活动的连接将被关闭。
    

#### Server块 (`server { ... }`)

定义了一个具体的服务，通常可以包含多个`server`块，分别配置不同的服务。

- **listen 8000;**：指定Nginx监听的端口号为8000。
    
- **gzip on;**：启用Gzip压缩，减小响应数据大小，加快传输速度。
    
- **gzip_min_length 1k;**：设置最小压缩长度为1KB，小于此长度的响应将不压缩。
    
- **gzip_comp_level 9;**：设置Gzip压缩级别，1到9之间，数字越大压缩率越高，但同时消耗更多CPU资源。9是最高压缩级别。
    
- **gzip_types ...;**：定义需要进行Gzip压缩的MIME类型，列出的类型的响应将被压缩。
    
- **gzip_vary on;**：为响应头添加`Vary: Accept-Encoding`，使缓存代理能正确识别压缩响应。
    
- **gzip_disable "MSIE [1-6].";**：禁止对IE6及以下版本启用Gzip压缩，因这些版本可能存在兼容性问题。
    

#### Location块 (`location / { ... }`)

定义了URL路径与本地文件系统或代理的映射关系。

- **location / { ... }**：匹配根路径（`/`）的请求。
    
    - **root html/dist;**：设置网站根目录为`html/dist`。
        
    - **index index.html index.htm;**：指定默认访问的文件，若目录下存在`index.html`或`index.htm`，则返回这些文件。
        
    - **try_files $uri $uri/ /index.html;**：按顺序尝试匹配请求的URI，如果找不到文件，则返回`index.html`，这通常用于单页应用（SPA），以便前端路由正常工作。
        

#### 自定义代理配置 (`location /aidss/api/system/ { ... }`)

- **location /aidss/api/system/ { ... }**：匹配`/aidss/api/system/`路径的请求。
    
    - **proxy_pass http://localhost:7529;**：将匹配到的请求代理到`http://localhost:7529`，即将请求转发给本地7529端口的服务。
        
    - **proxy_set_header X-Forwarded-Proto $scheme;**：在转发请求时，添加一个名为`X-Forwarded-Proto`的请求头，值为原始请求的协议（`http`或`https`）。
        
    - **proxy_set_header Host $http_host;**：在转发请求时，添加一个名为`Host`的请求头，值为原始请求的主机头。
        
    - **proxy_set_header X-Real-IP $remote_addr;**：在转发请求时，添加一个名为`X-Real-IP`的请求头，值为客户端的真实IP地址。
        

这个配置文件通常用于配置一个前端项目，利用Nginx处理静态文件，同时代理部分API请求到后端服务。
```

#### 4 运行和终止

运行命令：**nginx -c conf/nginx.conf**
然后使用浏览器来打开查看nginx的任务是否已经启动了

停止命令: **taskkill /f /t /im nginx.exe**
