---
layout: post
title: httpd.conf配置
date: 2020-09-08
author: 少年不年少
header-img: img/post/LBJ.jpg
catalog: true
tags:
    - Apache
    - 学习

---


# httpd.conf配置 #

> httpd.conf是Apache网络服务器软件中重要的一个配置文件，它存储着Apache中许多必不可少的配置信息。最常用的一点，就是向里面添加建站网站信息。

配置文件分三部分：

1. 全局配置文件——Global Environment
2. 主配置文件——'Main' server configuration
3. 虚拟主机——Virtual Hosts

## 全局配置文件

```
ServerRoot "/etc/httpd"
```

用于指定Apache的运行目录，服务启动之后自动将目录改变为当前目录，在后面使用到的所有相对路径都是想对这个目录下

```
PidFile run/httpd.pid
```

记录httpd守护进程的pid号码，这是系统识别一个进程的方法，系统中httpd进程可以有多个，但这个PID对应的进程是其他的父进程

```
Timeout 120
```

服务器与客户端与服务器断开的时间

```
KeepAlive Off
```

是否持续连接（因为每次连接都得三次握手，如果是访问量不大，建议打开此项，如果网站访问量比较大关闭此项比较好）

```
MaxKeepAliveRequests 100
```

表示一个连接的最大请求数

```
KeepAliveTimeout 15
```

断开连接前的时间

```
<IfModule prefork.c>

StartServers 8

MinSpareServers 5

maxSpareServers 20

ServerLimit 256

MaxClients 256

MaxRequestsPerChild 4000

</IfModule>
```

以上这几行是系统默认的模块儿，表示为每个访问启动一个进程（即当有多个连接公用一个进程的时候，在同一时刻只能有一个获得服务）。

```
StartServer
```

开始服务时启动8个进程，最小空闲5个进程，最多空闲20个进程。

```
MaxClient
```

限制同一时刻客户端的最大连接请求数量超过的要进入等候队列。

```
MaxRequestsPerChild
```

每个进程生存期内允许服务的最大请求数量，0表示永不结束

```
<IfModule worker.c>

ServerLimit 10

ThreadLimit 64

StartServers 2

MaxClients 250

MinSpareThreads 25

MaxSpareThreads 75

ThreadsPerChild 25

MaxRequestsPerChild 0

</IfModule>
```

以上几行是为Apache配置线程访问，即每对WEB服务访问启动一个线程，这样对内存占用率比较小。

```
ServerLimit
```

服务器允许配置进程数的上限。

```
ThreadLimit
```

每个子进程可能配置的线程上限

```
StartServers
```

启动两个httpd进程，

```
MaxClients
```

同时最多能发起250个访问，超过的要进入队列等待，其大小有ServerLimit和ThreadsPerChild的乘积决定

```
ThreadsPerChild
```

每个子进程生存期间常驻执行线程数，子线程建立之后将不再增加

```
MaxRequestsPerChild
```

每个进程启动的最大线程数，如达到限制数时进程将结束，如置为0则子线程永不结束

```
Listen 80
```

监听的端口，如有多块网卡，默认监听所有网卡

```
LoadModule .......
```

启动时加载的模块儿

```
Include conf.d/*.conf
```

加载的配置文件

```
User apache
Group apache
```

启动服务后转换的身份，在启动服务时通常以root身份，然后转换身份，这样增加系统安全。

## 主配置文件

```
ServerAdmin root@localhost
```

管理员的邮箱

```
ServerName localhost
```

默认是不需要指定的，服务器通过名字解析过程来获得自己的名字，但如果解析有问题（如反向解析不正确），或者没有DNS名字，也可以在这里指定IP地址，当这项不正确的时候服务器不能正常启动。

```
UseCanonicalName Off
```

如果客户端提供了主机名和端口，Apache将会使用客户端提供的这些信息来构建自引用URL。这些值与用于实现基于域名的虚拟主机的值相同，并且对于同样的客户端可用。CGI变量SERVER_NAME和SERVER_PORT也会由客户端提供的值来构建

```
DocumentRoot "/var/www/html"
```

网页文件存放的目录

```
<Directory />

Options FollowSymLinks

AllowOverride None

</Directory>
```

这是对根目录的一个权限的设置

```
<Directory "/var/www/html">

Options Indexes FollowSymLinks

AllowOverride None

Order allow,deny

Allow from all

</Directory>
```

同上 其中的含义是：

options中 Indexes表示当网页不存在的时候允许索引显示目录中的文件，FollowSymLinks是否允许访问符号链接文件。还有得选项有ExecCGI表是否使用CGI，SymLinksOwnerMatch表示当符号链接的文件和目标文件为同一用户拥有时才允许访问。等选项

AllowOverride None表示不允许这个目录下的访问控制文件来改变这里的配置，这也意味着不用查看这个目录下的访问控制文件。

Order 对页面的访问控制顺序 后面的一项是默认选项，如allow，deny则默认是deny

Allow from all 表示允许所有的用户，通过和上一项结合可以控制对网站的访问控制

```
<IfModule mod_userdir.c>

UserDir disable

</IfModule>
```

是否允许用户访问其家目录，默认是不允许

```
#<Directory /home/*/public_html>

# AllowOverride FileInfo AuthConfig Limit

# Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec

# <Limit GET POST OPTIONS>

# Order allow,deny

# Allow from all

# </Limit>

# <LimitExcept GET POST OPTIONS>

# Order deny,allow

# Deny from all

# </LimitExcept>

#</Directory>
```

如果允许访问用户的家目录中的网页文件，则取消以上注释，并对其中进行修改

```
DirectoryIndex index.html index.html.var
```

指定所要访问的主页的默认主页名字

```
AccessFileName .htaccess
```

定义每个目录下的访问控制文件名，缺省为.htaccess

```
<Files ~ "^/.ht">

Order allow,deny

Deny from all

</Files>
```

控制不让web上的用户来查看.htpasswd和.htaccess这两个文件

```
TypesConfig /etc/mime.types
```

用于设置保存有不同MIME类型数据的文件名，

```
DefaultType text/plain
```

默认的网页的类型

```
<IfModule mod_mime_magic.c>

# MIMEMagicFile /usr/share/magic.mime

MIMEMagicFile conf/magic
```

指定判断文件真实MIME类型功能的模块儿

```
</IfModule>

HostnameLookups Off
```

当打开此项功能时，在记录日志的时候同时记录主机名，这需要服务器来反向解析域名，增加了服务器的负载，通常不建议开启

```
#EnableMMAP off
```

是否允许内存映射：如果httpd在传送过程中需要读取一个文件的内容，它是否可以使用内存映射。如果为on表示如果操作系统支持的话，将使用内存映射。在一些多核处理器的系统上，这可能会降低性能，如果在挂载了NFS的DocumentRoot上如果开启此项功能，可能造成因为分段而造成httpd崩溃

```
#EnableSendfile off
```

这个指令控制[httpd](https://baike.baidu.com/item/httpd)是否可以使用操作系统内核的sendfile支持来将文件发送到客户端。默认情况下，当处理一个请求并不需要访问文件内部的数据时(比如发送一个静态的文件内容)，**如果操作系统支持，****Apache****将使用****sendfile****将文件内容直接发送到客户端而并不读取文件**

```
ErrorLog logs/error_log
```

错误日志存放的位置

```
LogLevel warn
```

Apache日志的级别

```
LogFormat "%h %l %u %t /"%r/" %>s %b /"%{Referer}i/" /"%{User-Agent}i/"" combined

LogFormat "%h %l %u %t /"%r/" %>s %b" common

LogFormat "%{Referer}i -> %U" referer

LogFormat "%{User-agent}i" agent
```

以上几个定义了日志的格式，并用不同的代号表示

```
CustomLog logs/access_log common

CustomLog logs/access_log combined
```

说明日志记录的位置，这里面使用了相对路径，所以ServerRoot需要指出

```
ServerSignature On
```

定义当客户请求的网页不存在，或者错误的时候是否提示服务器的版本的一些信息

```
Alias /icons/ "/var/www/icons/"
```

定义一些不在DocumentRoot下的文件，而可以将其映射到网页根目录中，这也是访问其他目录的一种方法，但在声明的时候切记目录后面加 ”/”

```
<Directory "/var/www/icons">

Options Indexes MultiViews

AllowOverride None

Order allow,deny

Allow from all

</Directory>
```

以上这几行定义了，对/var/www/icons/的权限

```
<IfModule mod_dav_fs.c>

# Location of the WebDAV lock database.

DAVLockDB /var/lib/dav/lockdb

</IfModule>
```

这是对mod_dav_fs.c模块儿的管理

```
ScriptAlias /cgi-bin/ "/var/www/cgi-bin/"
```

对CGI模块儿的的别名，与Alias相似。

```
<Directory "/var/www/cgi-bin">

AllowOverride None

Options None

Order allow,deny

Allow from all

</Directory>
```

对/var/www/cgi-bin文件夹的管理，方法同上

```
#Redirect old-URI new-URL
```


　　Redirect参数是用来重写URL的，当浏览器访问服务器上的一个已经不存在的资源的时候，服务器返回给浏览器新的URL，告诉浏览器从该URL中获取资源。这主要用于原来存在于服务器上的文档改变位置之后，又需要能够使用老URL能访问到原网页

```
AddLanguage zh-CN .zh-cn
```

添加语言

```
LanguagePriority en ca cs da de el eo es et fr he hr it ja ko ltz nl nn no pl pt pt-BR ru sv zh-CN zh-TW
```

Apache支持的语言

```
AddDefaultCharset UTF-8
```

默认支持的语言

```
#AddType application/x-tar .tgz
```

支持的应用如果想支持对php的解析添加这样一行

```
AddType application/x-httpd-php .php

#AddEncoding x-compress .Z

#AddEncoding x-gzip .gz .tgz
```

以上两行表示支持对以.Z和.gz .tgz结尾的文件

```
AddType application/x-gzip .gz .tgz
```

添加对上述两种文件的应用

```
AddType text/html .shtml

AddOutputFilter INCLUDES .shtml
```

添加动态处理类型为server-parsed由服务器预先分析网页内的标记，将标记改为正确的HTML标识

```
#ErrorDocument 404 /missing.html
```

当服务器出现404错误的时候，返回missing.html页面

```
Alias /error/ "/var/www/error/"
```

同理为赋值别名

```
<IfModule mod_negotiation.c>

<IfModule mod_include.c>

<Directory "/var/www/error">

AllowOverride None

Options IncludesNoExec

AddOutputFilter Includes html

AddHandler type-map var

Order allow,deny

Allow from all

LanguagePriority en es de fr

ForceLanguagePriority Prefer Fallback

</Directory>

```

以上为对/var/www/error网页的权限及操作

```
BrowserMatch
```

这里设置特殊的参数，以保证对老版本浏览器的兼容，并支持新浏览器的特性。

## 虚拟主机

```
NameVirtualHost *:80
```

这是虚拟主机的设置，如果启用虚拟主机的话，必须将前面的注释去掉，而且，第二部分的内容都可以出现在每个虚拟主机部分。

```
#<VirtualHost *:80>

# ServerAdmin webmaster@网址

# DocumentRoot /www/docs/网址

# ServerName 网址

# ErrorLog logs/网址-error_log

# CustomLog logs/网址-access_log common

#</VirtualHost>
```

