
通过理解 Laravel HTTP请求流程，不仅能够增加开发 Laravel 项目的自信心。还有助于调试项目、定位和解决bug。在某些场景下可以快速的解决问题

#### Laravel 执行过程
![Laravel](
https://i0.wp.com/blog.mallow-tech.com/wp-content/uploads/2016/06/Laravel-Request-Life-Cycle.png?resize=1024%2C559)

#### 处理HTTP请求
当public/index.php 接收到请求后，首先将[composer生成的自动加载](https://segmentfault.com/a/1190000014948542)引入项目，然后接下来的核心代码如下（忽略 HTTP 请求处理之外的代码）
```
$app = new Illuminate\Foundation\Application(
    realpath(__DIR__.'/../')
);

// 绑定处理 HTTP 请求的接口实现到服务容器
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);

// 从服务容器中解析处理 HTTP 请求的 Kernel 实例
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

// 处理 HTTP 请求的核心代码
$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

// 发送响应
$response->send();

// 终止程序，做一些善后及清理工作
$kernel->terminate($request, $response);
```
这段代码会创建一个 Application 实例，作为全局的服务容器，然后将处理请求的核心类 Kernel 实现实例绑定到该容器中，以便后续通过它处理 HTTP 请求。我们通过服务器捕获请求并将其传递给 Kernel 实例进行处理，处理结果是准备好的响应实例，调用该响应实例的 send() 方法即可将其发送给发起请求的客户端。最后，我们执行 Kernel 实例上的 terminate() 终止程序，退出脚本。
**所以核心逻辑在$kernel->handle() 方法调用中**

#### [什么是服务容器](https://xueyuanjun.com/post/9700)
在上面的代码中，$app 对应的就是服务容器实例，并且在我们获取到该实例后就注册了 Kernel 接口及其实现类到容器中:
```
$app->singleton(
    Illuminate\Contracts\Http\Kernel::class,
    App\Http\Kernel::class
);
```
singleton 方法会以单例方式在服务容器中将 App\Http\Kernel 实例绑定到 Illuminate\Contracts\Http\Kernel 接口，后续我们要获取 App\Http\Kernel 实例，就可以通过 Illuminate\Contracts\Http\Kernel 接口从服务容器中获取，获取方法是 $app->make()：
```
$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);
```

#### 查看Kernel代码
进入 $kernel->handle()，可以看到核心处理逻辑通过 sendRequestThroughRouter 方法实现：
```
protected function sendRequestThroughRouter($request)
{
    $this->app->instance('request', $request);

    Facade::clearResolvedInstance('request');

    $this->bootstrap();

    return (new Pipeline($this->app))
                ->send($request)
                ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)
                ->then($this->dispatchToRouter());
}
```
在发送请求到路由之前，先调用 bootstrap() 方法运用应用的启动类：
```
protected $bootstrappers = [
    \Illuminate\Foundation\Bootstrap\LoadEnvironmentVariables::class,
    \Illuminate\Foundation\Bootstrap\LoadConfiguration::class,
    \Illuminate\Foundation\Bootstrap\HandleExceptions::class,
    \Illuminate\Foundation\Bootstrap\RegisterFacades::class,
    \Illuminate\Foundation\Bootstrap\RegisterProviders::class,
    \Illuminate\Foundation\Bootstrap\BootProviders::class,
];
```
启动类相当于对整个应用进行初始化，看对应的类名就知道对应的功能，分别是加载环境变量和全局配置、配置异常处理逻辑、注册门面和服务提供者（根据 config/app.php 中的 providers 和 alias 配置值）、以及执行所有已注册服务提供者的 boot 方法。

HTTP 请求处理：
```
return (new Pipeline($this->app))
            ->send($request)
            ->through($this->app->shouldSkipMiddleware() ? [] : $this->middleware)
            ->then($this->dispatchToRouter());
```
Laravel 框架以[管道模式](https://xueyuanjun.com/post/3088.html)来处理 HTTP 请求，首先通过全局中间件对请求进行处理，如果返回 false 直接退出，不会做路由解析处理。全局中间件都校验通过才会将请求分发到路由器进行处理，路由器会将请求 URL 路径与应用注册的所有路由进行匹配，如果有匹配的路由，则先收集该路由所分配的所有路由中间件，通过这些路由中间件对请求进行过滤，所有路由中间件校验通过才会运行对应的匿名函数或控制器方法，执行相应的请求处理逻辑，最后准备好待发送给客户端的响应。


响应准备就绪后，就会通过 $response->send() 发送给发起请求的客户端，之后还要运行 $kernel->terminate() 做一些善后清理工作，并最终退出脚本。这些善后清理工作主要包括运行终止中间件，以及注册到服务容器的一些终止回调：
```
public function terminate($request, $response)
{
    $this->terminateMiddleware($request, $response);

    $this->app->terminate();
}
```
