---
title: Laradock 启动报错
categories:
- PHP
---

```
[ERROR] [FATAL] InnoDB: Table flags are 0 in the data dictionary but the flags in file ./ibdata1
```
遇到这个问题一般都是Laradock 切换版本导致的，只需要执行下面3个命令就能解决问题
<!--more-->
````
docker-compose down
rm -rf ~/.laradock/data/mysql

docker-compose up -d mysql
```
> 注意windows一般在C盘，用户/username/.laradock