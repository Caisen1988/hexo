---
title: Laravel路由用法
categories: 
- PHP
---
#### 路由入门
在 Laravel 应用中，定义路由有两个入口，一个是 `routes/web.php`，另外一个是`routes/api.php`，用于处理其他接入方的 API 请求（通常是跨语言、跨应用的请求），我们从第一个开始。
<!--more-->
最简单的一个路由，定义一个路径以及一个映射到该路径的闭包函数：
```
// routes/web.php 
Route::get('/', function () { 
    return 'Hello, World!'; 
});
```
这样，当我们访问应用首页 http://blog.test 时，就可以看到页面显示 Hello, World! 这一行字符串。这就是一个最简单的 Laravel 路由定义，但是涵盖了一个 Web 框架的基本功能：处理请求，返回响应。

#### 路由动作
常见路由四种动作: get, post, put, delete
```
Route::get('/', function () {}); 
Route::post('/', function () {}); 
Route::put('/', function () {});
Route::delete('/', function () {});
```
还可以通过 Route::any 定义一个可以捕获任何请求方式的路由：
```
Route::any('/', function () {});
```
从安全角度说，并不推荐上述这种路由定义方式，但是兼顾到便利性，我们可以通过 Route::match 指定请求方式白名单数组，比如下面这个路由可以匹配 GET 或 POST 请求：
```
Route::match(['get', 'post'], '/', function () {});
```
#### 复杂业务逻辑处理
当然，传递闭包并不是定义路由的唯一方式，闭包简单快捷，但是随着应用体量的增长，将日趋复杂的业务逻辑全部放到路由文件中显然是不合适的，另外，通过闭包定义路由也无法使用路由缓存（稍后会讲到）从而优化应用性能。对于稍微复杂一些的业务逻辑，我们可以将其拆分到控制器方法中实现，然后在定义路由的时候使用控制器+方法名来取代闭包函数：
```
Route::get('/', 'WelcomeController@index');
```

#### 路由参数
如果你定义的路由需要传递参数，只需要在路由路径中进行标识并将其传递到闭包函数即可：
```
Route::get('user/{id}', function ($id) {
    return "用户ID: " . $id;
});
```
这样，当你访问 http://blog.test/user/1000 的时候，就可以在浏览器看到 用户ID: 1000 字符串。
此外，你还可以定义可选的路由参数，只需要在参数后面加个 ? 标识符即可，同时你还可以为可选参数指定默认值：
```
Route::get('user/{id?}', function ($id = 1) {
    return "用户ID: " . $id;
});
```
这样，如果不传递任何参数访问 http://blog.test/user，则会使用默认值 1 作为用户 ID。
更高级的，你还可以为路由参数指定正则匹配规则：
```
Route::get('page/{id}', function ($id) {
    return '页面ID: ' . $id;
})->where('id', '[0-9]+');

Route::get('page/{name}', function ($name) {
    return '页面名称: ' . $name;
})->where('name', '[A-Za-z]+');

Route::get('page/{id}/{slug}', function ($id, $slug) {
    return $id . ':' . $slug;
})->where(['id' => '[0-9]+', 'slug' => '[A-Za-z]+']);
```
如果传入的路由参数与指定正则不匹配，则会返回 404 页面：
![](https://imgkr.cn-bj.ufileos.com/f695f157-2279-4625-b270-414784c203d0.png)
#### 路由命名，视图中使用路由
在应用其他地方引用路由的最简单的方式就是通过定义路由的第一个路径参数，你可以在视图中通过辅助函数 url() 来引用指定路由，该函数会为传入路径加上完整的域名前缀，所以 url('/') 对应的输出是 localhost.test。你可以在视图文件中这么使用：
```
<a href="{{ url('/') }}">
```
此外，Laravel 还允许你为每个路由命名，这样一来，不必显式引用路径 URL 就可以对路由进行引用，这样做的好处是你可以为一些复杂的路由路径定义一个简单的路由名称从而简化对路由的引用，另一个更大的好处是即使你调整了路由路径（在复杂应用中可能很常见），只要路由名称不变，那么就无需修改前端视图代码，提高了系统的可维护性。

路由命名很简单，只需在原来路由定义的基础上以方法链的形式新增一个 name 方法调用即可：
```
Route::get('user/{id?}', function ($id = 1) {
    return "用户ID: " . $id;
})->name('user.profile');
```
前端视图模板中可以通过辅助函数 route 并传入路由名称（如果有路由参数，则以数组方式作为第二个参数传入）来引用该路由：
```
<a href="{{ route('user.profile', ['id' => 100]) }}">
// 输出：http://blog.test/user/100
```
如果没有路由参数，通过 route('user.profile') 引用即可。此外，我们还可以简化对路由参数的传递，比如上例可以简化为：
```
<a href="{{ route('user.profile', [100]) }}">
```
#### 路由分组
所谓路由分组，其实就是通过 Route::group 将几个路由聚合到一起，然后给它们应用对应的共享特征：
```
Route::group([], function () { 
    Route::get('hello', function () { return 'Hello'; }); 
    Route::get('world', function () { return 'World'; }); 
});
```
由于没有应用任何共享特征（第一个参数是空数组），所以这样的路由分组其实并没有什么意义，下面我们来介绍一些常见的共享特征设置。
###### 中间件
我们使用路由分组最常见的场景恐怕就是为一组路由应用共同的中间件了，比如以认证中间件（别名auth）为例，如果用户已经认证可以进行后续处理，否则将会把用户重定向到登录页面。
```
Route::middleware('auth')->group(function () {
    Route::get('dashboard', function () {
        return view('dashboard');
    });
    Route::get('account', function () {
        return view('account');
    });
});
```
如果是多个中间件，可以通过数组方式传递参数，比如 ['auth', 'another']
```
Route::middleware(['auth', 'another'])->group(function () {
    //
});
```
###### 路由路径前缀
如果某些路由拥有共同的路径前缀，例如，所有 API 路由都以 /api 前缀开头，我们可以使用 Route::prefix 为这个分组路由指定路径前缀并对其进行分组：
```
Route::prefix('api')->group(function () {
    Route::get('/', function () {
        // 处理 /api 路由
    })->name('api.index');
    Route::get('users', function () {
        // 处理 /api/users 路由
    })->name('api.users');
});
```
###### 子域名路由
子域名路由和路由路径前缀一样，不过是通过子域名而非路径前缀对分组路由进行约束，子域名路由有两个使用场景，一个是为应用子系统设置不同的子域名：
```
Route::domain('admin.blog.test')->group(function () {
    Route::get('/', function () {
        // 处理 http://admin.blog.test 路由
    });
});
```
另一个是通过参数方式设置子域名，适用于网站拥有多租户的场景（比如天猫，顶级知名商家拥有自己独立的子域名，如 xiaomi.tmall.com）：
```
Route::domain('{account}.blog.test')->group(function () {
    Route::get('/', function ($account) {
        //
    });
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```
###### 子命名空间
以控制器方式定义路由的时候，当我们没有显式指定控制器的命名空间时，默认的命名空间是 `App\Http\Controllers`（在 `app/Providers/RouteServiceProvider.php` 中设置），如果某些控制器位于这个命名空间下的子命名空间中，该如何设置分组规则呢？我们可以通过 `Route::namespace` 为同一子命名空间下的分组路由设置共同的子命名空间：
```
Route::get('/', 'Controller@index');

Route::namespace('Admin')->group(function() {
     // App\Http\Controllers\Admin\AdminController
     Route::get('/admin', 'AdminController@index');
});
```
###### 路由命名前缀
除了通过上述共同特征对路由进行分组外，对于某一类资源路由，比如用户，往往拥有相同的路由命名前缀，如 user.，我们还可以基于这一特征对路由进行分组，使用 `Route::name` 方法即可实现：
```
// 路由命名+路径前缀
Route::name('user.')->prefix('user')->group(function () {
    Route::get('{id?}', function ($id = 1) {
        // 处理 /user/{id} 路由，路由命名为 user.show
        return route('user.show');
    })->name('show');
    Route::get('posts', function () {
        // 处理 /user/posts 路由，路由命名为 user.posts
    })->name('posts');
});
```
#### 路由执行相关源码跟读
