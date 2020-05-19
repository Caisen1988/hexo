---
title: Laradock(持续更新)
categories:
- Tool
---

还在为本地开发环境而烦恼吗？快使用Laradock吧，PHP版本,Nginx多站点,Composer,Node,NPM,Xdebug 通通不用担心，Laradock全部为你搞定，你只需要一台window电脑并安装好docker和Git就可以。
<!--more-->

#### 1.多个项目使用Laradock
1.1 下载Laradock仓库
```
git clone https://github.com/laradock/laradock.git
```
1.2 文件目录结构
 ```
* laradock
* project-1
* project-2
 ```
 1.3 进入laradock/nginx/sites目录，里面已经有了3个默认的配置文件
`app.conf.example, laravel.conf.example and symfony.conf.example.`
1.4 下载image并生成container启动, 这一步最好换成国内源，需要一些时间安装
```
docker-compose up -d nginx php-fpm mysql
```
https://hub-mirror.c.163.com
![](/images/2354.png)

1.5 进入container执行Artisan, Composer, PHPUnit, Gulp, …等操作
```
docker-compose exec workspace bash  //Linux or Macos
docker exec -it {workspace-container-id} bash  //windows
```

1.6 进入 laradock/nginx/sites 文件夹中
```
cp laravel.conf.example laravel1.conf
cp laravel.conf.example laravel2.conf
```
1.7 修改 laravel1.conf 和 laravel2.conf
```
#laravel1.conf
     server_name laravel1.test;
     root /var/www/laravel1/public;
 #laravel2.conf
     server_name laravel2.test;
     root /var/www/laravel2/public;
```
1.8 修改laradock的.env
`APP_CODE_PATH_HOST=../sites/`

1.9 修改hosts文件 #本地url地址重定向
```
127.0.0.1 laravel1.test
127.0.0.1 laravel2.test
```

然后访问浏览器访问laravel1.test和laravel2.test，就可以开发调试了

#### 2. 进入container