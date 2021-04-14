# Win10中使用Docker搭建Django开发环境

## 下载链接

[Docker](https://hub.docker.com/)

## 安装

无脑安装

- 问题

使用docker需要启用hyper-v，默认情况下开启hyper-v需要打开bios的虚拟功能

## docker常用命令

- 容器的状态（7种）

  - created（已创建）
  - restarting（重启中）
  - running（运行中）
  - removing（迁移中）
  - paused（暂停）
  - exited（停止）
  - dead（死亡

- docker常用命令

  ```shell
  docker --version  # 产看docker版本
  docker ps         # 查看运行的容器
  docker pull ubuntu  # 获取镜像
  docker search [httpd] # 查找镜像
  docker rmi [hello-world]  # 删除镜像
  docker rm [容器ID]  # 删除容器
  docker image ls   # 显示所有镜像
  docker stop [容器ID或容器名]  # 停止一个正在运行的容器
  docker kill [容器ID或容器名]  # 直接关闭容器
  docker restart 容器id  # 重启容器
  docker attach 容器ID  # 进入容器
  docker run -it -p 8080:80 镜像ID  # 将外部宿主机的8080端口映射到docker内部80端口，外部端口可以多对一或一对一映射到内部端口，但不能一对多映射到内部端口
  docker exec -it 784e12ee483d  /bin/bash  # 进入容器
  ```

- docker批量操作命令

  ```shell
  docker stop $(docker ps -q)  # 停止所有容器
  docker rm $(docker ps -aq)   # 删除所有停止容器
  docker stop $(docker ps -q) & docker rm $(docker ps -aq) # 删除所有容器
  ```

  

## 搭建基础开发环境

1. 使用ubuntu作为基础镜像

   ```shell
   docker pull ubuntu:16.04  # 拉取ubuntu：16.04镜像
   docker run -it ubuntu     # 以交互式方式运行ubuntu
   ```

   

2. 在ubuntu系统中配置django开发环境(不用这么麻烦，见3)

   ```shell
   apt-get update  # 更新源
   apt-get install python3  # 要加3，不然安装的是python2.x版本
   apt-get install python3-pip  #安装pip
   ```

3. 以python镜像为基础，搭建django开发环境

   ```
   docker pull python:3.7  # 拉取python3.7官方镜像
   docker run -it python:3.7 /bin/bash  # 以交互是方式运行该镜像，其实建立了容器
   ```

4. 打包为镜像

   ```
   docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
   OPTIONS说明：
   
   -a :提交的镜像作者；
   
   -c :使用Dockerfile指令来创建镜像；
   
   -m :提交时的说明文字；
   
   -p :在commit时，将容器暂停。
   ```

5. 在服务器上安装docker

6. 拉取自己制作的镜像

7. 创建容器并建立端口映射

8. 安装ssh-server

## 制作好镜像后

1. 拉取镜像

   ```shell
   docker pull zfzdr/django:1.2
   ```

2. 创建容器并端口映射

   ```shell
   docker run -it -p 22:22 -p 8000:8000 zfzdr/django:1.2
   ```

3. 创建密码

   ```shell
   passwd
   ```

4. 配置pycharm

   - python解释器配置

   ![image-20200802111732688](/assets/img/image-20200802111732688.png)

   - 运行环境配置

![image-20200802111500006](/assets/img/image-20200802111500006.png)

## SSH连接docker容器

1. 创建容器并端口映射

   ```shell
   docker run -it -p 22:22 -p 8000:8000 -p 8080:8080 zfzdr/django:1.2
   ```

2. 安装ssh，修改配置，设置开机自启动

   ```shell
   vim /etc/ssh/sshd_config  # 修改配置文件
   ```

   ![image-20200803091513258](/assets/img/image-20200803091513258.png)

   

   - 重启ssh服务，并设置开机启动：

   ```shell
   $  /etc/init.d/ssh  restart  # 重启
   
   # 开机自动启动ssh命令
   sudo systemctl enable ssh
   
   # 关闭ssh开机自动启动命令
   sudo systemctl disable ssh
   
   # 单次开启ssh
   sudo systemctl start ssh
   
   # 单次关闭ssh
   sudo systemctl stop ssh
   
   # 设置好后重启系统
   reboot
   
   #查看ssh是否启动，看到Active: active (running)即表示成功
   sudo systemctl status ssh
   
   ```

3. pycharm使用ssh连接本地docker

   



# 安装django-mdeditor和markdown

```
pip install django-mdeditor  # 安装插件
pip install markdown
```

- 添加mdeditor

```bash
    INSTALLED_APPS = [
        ...
        'mdeditor',
    ]
```

# 安装nginx和uwsgi[参考](https://www.cnblogs.com/zouzou-busy/p/11625994.html)

1. 安装nginx软件并测试

```shell
sudo apt-get install python-dev nginx  # 安装
nginx -v # 查看nginx安装版本
```

Nginx的默认端口为80，很容易被其他服务占用，因此需要修改成其他端口。打开文件

```
vim /etc/nginx/nginx.conf 
```

如果能找到监听的端口，直接修改 。找不到的也没关系 ，找到如下配置 ，说明是引用了其他目录的文件

```
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

打开 /etc/nginx/sites-enabled/ ，找到并编辑 default ，将80修改成8080

```
# Default server configuration
#
server {
    listen 8088 default_server;
    listen [::]:8088 default_server;

    ...........
```

Nginx启动命令

```
service nginx start

service nginx restart

service nginx stop

```

![image-20200805110106915](/assets/img/image-20200805110106915.png)

1. 安装usgi

   ```
   sudo pip install uwsgi --upgrade
   ```

2. 测试uwsgi运行状态

   - 新建文件test.py

```python
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return [b"Hello World"]
```

   - 使用uwsgi运行该文件

     ```
     uwsgi --http :8080 --wsgi-file test.py  # 端口可以改变
     ```

![image-20200805103645716](/assets/img/image-20200805103645716.png)

此语句的意思是，使用uwsgi运行test.py文件， 采用http模式， 端口8000

4. uwsgi运行项目

确保当前的路径为项目根目录下，也就是和manage.py文件同级，注意--http后面有空格

```shell
uwsgi --http :8080 ---module myblog.wsgi
```

- --http  指明是http协议
- :9001 指定端口号
- --module Apitest.wsgi 加载指定的wsgi模块，根据自己的项目名称自己指定

访问我们的django自带的后台，静态文件不加载

![image-20200805133326448](/assets/img/image-20200805133326448.png)



 补充：大家都知道pycharm会自动检测django项目，如果代码有变动，会自动重启，uswgi也可以，加上一个参数就可以了

```shell
uwsgi --http :8080 --module myblog.wsgi --py-autoreload=1
```

多次启动会遇到端口占用问题，解决方法如下：

```
lsof -i :8080  #产看8080端口占用情况
kill -9 pid   # 使用 kill 命令即可杀掉该进程
```

5. 通过配置文件启动uwsgi

在根目录 /home/zfzdr/myblog-web下面新建一个uwsgi_conf文件夹下，然后在uwsgi_conf文件夹下新建uwsgi.ini文件

```ini
[uwsgi]
#使用HTTP访问的端口号, 使用这个端口号是直接访问了uWSGI, 绕过了Nginx
http = :8080

# 如果你已经配置了nginx(启动了nginx服务，配置了uwsgi_pass)，请用这个socket连接
socket = 0.0.0.0:8001
#是否使用主线程
master = true

# 项目在服务器中的目录(绝对路径)
chdir = /home/zfzdr/myblog-web

module = myblog.wsgi

;static-map=/static=%(chdir)/%(media)  # 仅加载media文件
;static-map=/static=%(chdir)/%(collect_static) # 仅加载static文件
;for =collect_static media
;static-map=/static=%(chdir)/%(_)
;endfor =
# 设置日志目录
daemonize=%(chdir)/uwsgi_conf/uwsgi.log
# uWSGI进程号存放
pidfile=%(chdir)/uwsgi_conf/uwsgi.pid

# Django's wsgi 文件目录
#wsgi-file = project_name/wsgi.py
# 指明通过uwsgi启动最大进程数
processes = 4
#每个进程的线程数
threads = 2
#状态监听端口
stats = 127.0.0.1:9191
# 退出时自动清理环境配置
vacuum = true 
#目录下文件改动时自动重启
touch-reload = /home/zfzdr/Web开发/myblog-web
#Python文件改动时自动重启
py-auto-reload = 1
#后台运行并把日志存到.log文件
;daemonize = /home/zfzdr/Web开发/myblog/uWSGI.log
```

通过配置文件启动uwsgi

```shell
uwsgi --ini uwsgi_conf/uwsgi.ini
```

注意uwsgi不会处理静态文件，如果有，也是浏览器的缓存。如果想uwsgi也加载静态文件，可以通过static-map指定静态文件位置

```ini
static-map=/static=%(chdir)/%(media)  # 仅加载media文件
static-map=/static=%(chdir)/%(collect_static) # 仅加载static文件
```

6. .配置django的settings.py，收集所有myblog项目所需的静态文件

在settings.py配置文件加下面一行

```
STATIC_ROOT = os.path.join(BASE_DIR, "collect_static")
```

执行命令

```
python manage.py collectstatic
```

## 配置nginx

配置一个网站入口，当用户访问192.168.88.67:80 这个web应用时，自动将请求转发给uwsgi，uwsgi处理后，返回给nginx，返回给用户

当请求是192.168.88.67:80的时候，其实访问的是192.168.88.67:9999 这是动态请求，因为我是找到的uwsgi

nginx.conf配置如下

```
upstream blog_app {
   # nginx通过socket在环回接口地址的9001端口与本地的uWSGI进程通信
   # 支持ip:port模式以及socket file模式
   #server unix:/opt/mywebapp/uwsgi_conf/uwsgi.sock;
   server 127.0.0.1:8001;
}


server {
    listen 8080;
    #server_name ;
    # 接入报告日志
    access_log /home/zfzdr/myblog-web/nginx_conf/access.log;
    # 错误日志
    error_log /home/zfzdr/myblog-web/nginx_conf/error.log;
    charset utf-8;

    gzip_types text/plain application/x-javascript text/css text/javascript application/x-httpd-php application/json text/json image/jpeg image/gif image/png application/octet-stream;
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;

    location /{
        # nginx转发动态请求到uWSGI
        include /home/zfzdr/myblog-web/uwsgi_conf/uwsgi_params;
        #uwsgi_connect_timeout 20;　
        uwsgi_pass blog_app;
    }

    # 如果写成/static/,nginx无法找到项目静态文件路径
    location /static {
        alias /home/zfzdr/myblog-web/collect_static/;
    }

    # 如果写成/media/,nginx无法找到项目媒体文件路径
    location /media {
        alias  /home/zfzdr/myblog-web/media/;
    }

}
```

- 检查配置文件，加载配置文件

  ```shell
  nginx -t
  nginx -s reload
  nginx
  ```

  如果访问时出现的是nginx官方页面，很有可能是没有加载到自己的配置，将sites- 下的配置删掉，就能访问到自己的网站了

  # 实现容器开机自启动的方法

  在root下新建shell脚本run.sh

  ```shell
  #! /bin/bash
  /etc/init.d/ssh start
  /bin/bash
  ```

  添加执行权限

  ```shell
  chmod +x run.sh
  ```

  生成镜像，启动时运行run.sh

  ```shell
  docker run -it -p 22:22 -p 8000:8000 -p 8080:8080 zfzdr/django:1.5 /root/run.sh # 在新容器里面执行/root/run.sh 命令，即执行上面的脚本命令
  ```

  