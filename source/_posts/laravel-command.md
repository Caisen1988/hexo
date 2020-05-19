---
title: Laravel 常用命令行
categories:
- PHP
---

不管是 Node.js、Python 还是 PHP 的 Web 框架，都提供了通过命令行与应用进行交互的功能，通过这些命令行工具，我们可以完成一些初始化操作，比如创建新应用、执行数据库迁移、或者快速创建类文件等，在 Laravel 中，主要有以下三种工具场景
<!--more-->

- Laravel 安装器
- Tinker：一个由 PsySH 扩展包驱动的 REPL，允许你通过命令行与整个 Laravel 应用进行交互；
- Artisan：Laravel 内置的命令行操作工具集，支持自定义命令；

#### 1. Laravel 安装器
   1.1通过 Composer 安装 Laravel 安装器：
   
```
    composer global require "laravel/installer"
    composer global update  //如果之前已经安装过旧版本的 Laravel 安装器，需要更新后才能安装最新的 Laravel框架
```
   >确保 $HOME/.composer/vendor/bin 在系统路径中（Mac中对应路径是 ~/.composer/vendor/bin，Windows对应路径是 ~/AppData/Roaming/Composer/vendor/bin，其中 ~ 表示当前用户家目录），否则不能在命令行任意路径下调用 laravel 命令。
    
   ```
   laravel -v    //确保Laravel 全局安装成功
   laravel new blog  //在当前目录下创建一个名为 blog的 Laravel 应用
   ```

  1.2 当然可以继续通过 Composer Create-Project安装
  ```
    composer create-project --prefer-dist laravel/laravel blog
    composer create-project --prefer-dist laravel/laravel blog 5.6.* //安装指定版本的Laravel
  ```
#### 2. Tinker
2.1 Tinker介绍：
   tinker 是一个 REPL (read-eval-print-loop)，REPL 是指交互式命令行界面，它可以让你输入一段代码去执行，并把执行结果直接打印到命令行界面里， 这样可以很方便的看到数据库中的数据并且执行各种想要的操作和调试数据库中的数据，从而摆脱通篇都是 print_r() 和 dd() 的痛苦。

2.2 进入thinker
  ```
      php artisan tinker
      //使用模型工厂来创建10个User
           factory(App\User::class, 10)->create();
      //查看所有User
           App\User::all();
      //统计User
           App\User::count();
        
      //创建一个User
           $user = new App\User;
           $user->username = "Hello World";
           $user->email = "Hello_World@qq.com";
           $user->save();
           
      //删除User
           $user = App\User::find(712); //要删除 id 为 1 的用户：
           $user->delete();
           
      //查看源码
           show <functionName>
  ```

#### 3. Artisan常用命令: 
   artisan有非常多命令，大家可以先熟练运用下面几类常见的命令，比如创建model, control, request 和数据表迁移
```
    php artisan list
    php artisan help make      //帮助命令
    php artisan --version        //查看Laravel 版本
    php artisan serve --port=8080            //启动PHP内置的开发服务器 http://localhost:8080
    php artisan route:list
    php artisan make:controller StudentController
    php artisan make:controller PhotoController --resource //创建Rest风格资源控制器（带有index、create、store、edit、update、destroy、show方法）
    php artisan make:model Student  //只创建对应的model 文件，还没有创建对应的table
    php artisan make:migration create_users_table--create=students //创建students表
    php artisan make:modelStudent-m //创建模型的时候同时生成新建表的迁移
    php artisan migrate:rollback //回滚上一次的迁移
    php artisan migrate:reset  //回滚所有迁移
```