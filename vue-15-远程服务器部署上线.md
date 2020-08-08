# 上线部署（重点）

网站开发好了之后，还需要上线，这样别人才能够通过浏览器访问到我们的网站。

所以我们至少要租一台服务器，很多机构扮演着服务器供应商角色，比如阿里云、百度云、新浪云、腾讯云、华为云、京东云、西部数据、网易云、浪潮云、金山云、滴滴云、新网、万网等等，他们的购买流程和配置方法几乎都是一样的。

## 配置

购买的服务器其实就是一台电脑，本地怎么配置网站，服务器中就是怎么配置网站。

服务器购买完毕后，供应商会给我们这台服务器的ip、端口、用户名、密码等相关信息，我们在本地电脑中，使用远程桌面连接工具，就可以连接到这台服务器了，然后就在这台服务器中安装 Nginx、NodeJS、Apache这类工具，然后把网站配置上，别人就能访问我们的网站了。

## 连接

在本地电脑中，使用远程桌面连接工具，就可以连接到服务器了，然后正常操作服务器即可。

开始 -> 运行 -> mstsc （打开远程桌面连接后，输入ip和用户名、密码登录即可）。


# FTP

FTP 是文件传输协议，使用该协议，可以把本地文件上传到服务器中，也可以把服务器上的文件下载到本地。

我们通常在服务器端安装 FTP 服务器，比如在阿里云购买的服务器上，安装 File Zilla Server。

在本地自己的电脑中安装 FTP 客户端，安装 File Zilla。

https://www.filezilla.cn/download/


## 服务器

服务器需要设置站点

## 客户端

客户端可以连接服务器设置的站点，然后直接在窗口中选择文件，上传下载即可。


# Nginx 配置（重点）

市面上有很多服务器部署环境，比如 Nginx、IIS、Apache、Tomcat、Nodejs等等。

他们的作用都是提供网站环境，开发人员把开发好的网站放入到网站环境下，别的用户就可以通过浏览器访问到我们的网站了。

这些技术早期的时候各有各的特点，但现在性能上越来越趋同：

- Nginx是静态环境
- IIS是asp和aspx环境
- Apache是php环境
- Tomcat是静态环境
- Nodejs是js环境。

Nginx 是用的比较多的一个技术，它的优势是体积小、速度快、高并发支持的好。

Nginx 可以在在多种平台下使用，比如在 mac、linux、windows 下都可以使用。

我们这里讲的是在 windows 下的使用。

## 下载 nginx

http://nginx.org/en/download.html         

下载稳定版本，以nginx/Windows-1.12.2为例，直接下载 http://nginx.org/download/nginx-1.12.2.zip

下载后解压，解压后如下

```bash
# nginx-1.12.2
conf
contrib
docs
html
logs
temp
nginx.exe
```

## 启动 nginx

有多种启动 nginx 的方法

- 直接双击 nginx.exe，双击后一个黑色的弹窗一闪而过。
- 打开 cmd 命令窗口，切换到 nginx 解压目录下，输入命令 nginx.exe 或者 start nginx ，回车即可。

> 不要把 nginx 放到中文目录下

## 检查 nginx 是否启动成功

直接在浏览器地址栏输入网址 http://localhost:80，回车，如果能够正常打开页面，则说明启动成功。

## nginx 的配置文件

nginx 的配置文件是 conf 目录下的 nginx.conf，默认配置的 nginx 监听的端口为 80，如果 80 端口被占用可以修改为未被占用的端口即可。

```
http {

    server {
        listen  80;
        server_name localhost;
    }
}
```

> 检查80端口是否被占用的命令是： netstat -ano | findstr 0.0.0.0:80 或 netstat -ano | findstr "80"

## 重启 nginx

当我们修改了 nginx 的配置文件 nginx.conf 时，不需要关闭 nginx 后重新启动 nginx，只需要执行命令 nginx -s reload 即可让改动生效

```bash
nginx -s reload
```

> 修改目录后，如果浏览器没有把新站点显示出来，那么使用 taskkill 先关闭，再开启。

## 关闭 nginx

如果使用 cmd 命令窗口启动 nginx，关闭 cmd 窗口是不能结束 nginx 进程的，可使用两种方法关闭 nginx

- nginx -s stop(快速停止nginx)  或  nginx -s quit(完整有序的停止nginx)
- taskkill /f /t /im nginx.exe

## 代理服务器

我们可以修改 nginx 的配置文件 nginx.conf 达到访问 nginx 代理服务器时跳转到指定服务器的目的，即通过 proxy_pass 配置请求转发地址，即当我们依然输入http://localhost:80 时，请求会跳转到我们配置的服务器：

```
upstream abc_server{
    server localhost:8080;
}

server {
    listen 80;
    server_name localhost;
    location / {
        proxy_pass http://abc_server;
    }
}
```

nginx.conf 修改完毕后，记得重启 nginx，还要记得做一个 8080 的网站出来，这样用户在访问 80 时，实际上访问的是 8080 这个网站，这就叫做代理服务器。

## 负载均衡

一个网站的访问人群特别多时，可能同一时间有特别多的人同时访问，这就叫做高并发，如果不对这种高并发现象做特殊处理的话，就会造成用户访问的排队，页面响应的卡顿，用户体验会比较糟糕。

负载均衡就是解决高并发的一种常见方案。

原理是：开发人员做多台服务器，每台服务器上有一模一样的网站，就算有很多人同时访问我们的网站，我们通过上文讲到的代理技术，让用户访问另一台服务器中的网站，这样就不会排队了。

> 我们可以配置多个目标服务器，当一台服务器出现故障时，nginx能将请求自动转向另一台服务器。

```
upstream abc_server{
    server localhost:8080 weight=2;
    server 192.168.100.103:8081 weight=1;
}

server {
    listen 80;
    server_name localhost;
    location / {
        proxy_pass http://abc_server;
    }
}
```

> weight属性，此属性表示各服务器被访问到的权重，weight越高被访问到的几率越高。


## 配置静态资源

最传统的网站部署方式，比如我们的网站开发完毕后，该项目在 d:/www 下，那么直接在 nginx 中配置该路径，浏览器就可以直接访问了。

```
server {
    listen  80;
    server_name localhost;
    location / {
        root d:/www;
        index index.html index.htm;
    }
}
```

## 虚拟目录（多站点）

一台服务器下是否可以使用nginx建立多站点呢？（答案是可以的）

**step 1: 建立虚拟目录并编写规则文件**

在 nginx 根目录下建立 vhost 文件夹

里面建立 vhost_siteA.conf 和 vhost_siteB.conf 两个文件

> 每一个网站，都单独做一个 conf 配置文件，里面的内容几乎一致。

```
nginx/
    vhost/
        vhost_siteA.conf
        vhost_siteB.conf
```

vhost_siteA.conf 的内容

```
server {
	listen	8090;

    location / {
        root	C:\Users\wangyang\Desktop\web0123;
        index	index.html;
    }	
}
```

> 其他站点的内容照着配一下，把端口和路径改了即可。


**step 2: 引入配置文件**

在 nginx 的根目录，打开 conf/nginx.conf

在 http 的子一层新增 include，把路径指过来（注意斜杠）

```
http {
    server {

    }
	include C:/Users/wangyang/Desktop/nginx-1.12.2/nginx-1.12.2/vhost/*.conf;
}
```

> 使用 taskkill 关闭服务，然后重启，注意中文路径。不要用IE测试。