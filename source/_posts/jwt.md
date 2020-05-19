---
title: Laravel使用JWT无感知刷新token请求API实现前后端分离
categories: 
- PHP
---

#### 背景知识讲解
> https://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html

通过这篇文章，我们将使用JWT(JSON Web Tokens)来是实现无感知刷新token请求API。
<!--more-->
#### 创建新的项目
通过运行下面的命令，我们就可以开始并创建新的 Laravel 项目。

```
composer create-project --prefer-dist laravel/laravel jwt
```
这会在名为 jwt 的目录下创建一个新的 Laravel 项目


#### 配置 JWT 扩展包

##### 安装 tymon/jwt-auth 扩展包

```
composer require tymon/jwt-auth:dev-develop --prefer-source
```
##### 发布配置文件
```
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```
##### 生成 JWT 密钥
JWT 令牌通过一个加密的密钥来签发。
```
php artisan jwt:secret
```
##### 注册中间件
JWT 认证扩展包附带了允许我们使用的中间件。在 app/Http/Kernel.php 中注册 auth.jwt 中间件：
```
protected $routeMiddleware = [ 
.... 
'auth.jwt' => \Tymon\JWTAuth\Http\Middleware\Authenticate::class, ];
```
这个中间件会通过检查请求中附带的令牌来校验用户的认证。如果用户未认证，这个中间件会抛出 UnauthorizedHttpException 异常。

#### 设置路由，加上中间件。
除了login, register以外的其他API, 请求的时候header里都必须要带token, token验证不通过会抛错
```
Route::post('login', 'ApiController@login');
Route::post('register', 'ApiController@register');

Route::group(['middleware' => 'auth.jwt'], function () {
    Route::get('logout', 'ApiController@logout');
    //其他API
});

```

#### 更新User 模型，添加以下方法
JWT 需要在 User 模型中实现 Tymon\JWTAuth\Contracts\JWTSubject 接口。 此接口需要实现两个方法  getJWTIdentifier 和 getJWTCustomClaims
```
<?php

namespace App;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;
use Tymon\JWTAuth\Contracts\JWTSubject;

class User extends Authenticatable implements JWTSubject
{
    use Notifiable;

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];

    /**
     * Get the identifier that will be stored in the subject claim of the JWT.
     *
     * @return mixed
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * Return a key value array, containing any custom claims to be added to the JWT.
     *
     * @return array
     */
    public function getJWTCustomClaims()
    {
        return [];
    }
}
```
#### JWT 身份验证逻辑讲解

用户注册时需要姓名，邮箱和密码。那么，让我们创建一个表单请求来验证数据。通过运行以下命令创建名为 RegisterAuthRequest 的表单请求：
```
php artisan make:request RegisterAuthRequest
```
它将在 app/Http/Requests 目录下创建 RegisterAuthRequest.php 文件。将下面的代码黏贴至该文件中。
```
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class RegisterAuthRequest extends FormRequest
{
    /**
     * 确定是否授权用户发出此请求
     *
     * @return bool
     */
    public function authorize()
    {
        return true;
    }

    /**
     * 获取应用于请求的验证规则
     *
     * @return array
     */
    public function rules()
    {
        return [
            'name' => 'required|string',
            'email' => 'required|email|unique:users',
            'password' => 'required|string|min:6|max:10'
        ];
    }
}
```

运行以下命令创建一个新的 ApiController :
```
php artisan make:controller ApiController
```
这将会在 app/Http/Controllers 目录下创建 UserController.php 文件。将下面的代码黏贴至该文件中。
```
<?php

namespace App\Http\Controllers;

use App\Http\Requests\RegisterAuthRequest;
use App\User;
use Illuminate\Http\Request;
use JWTAuth;
use Tymon\JWTAuth\Exceptions\JWTException;

class ApiController extends Controller
{
    public $loginAfterSignUp = true;

    public function register(RegisterAuthRequest $request)
    {
        $user = new User();
        $user->name = $request->name;
        $user->email = $request->email;
        $user->password = bcrypt($request->password);
        $user->save();

        if ($this->loginAfterSignUp) {
            return $this->login($request);
        }

        return response()->json([
            'success' => true,
            'data' => $user
        ], 200);
    }

    public function login(Request $request)
    {
        $input = $request->only('email', 'password');
        $jwt_token = null;

        if (!$jwt_token = JWTAuth::attempt($input)) {
            return response()->json([
                'success' => false,
                'message' => 'Invalid Email or Password',
            ], 401);
        }

        return response()->json([
            'success' => true,
            'token' => $jwt_token,
        ]);
    }

    public function logout(Request $request)
    {
        $this->validate($request, [
            'token' => 'required'
        ]);

        try {
            JWTAuth::invalidate($request->token);

            return response()->json([
                'success' => true,
                'message' => 'User logged out successfully'
            ]);
        } catch (JWTException $exception) {
            return response()->json([
                'success' => false,
                'message' => 'Sorry, the user cannot be logged out'
            ], 500);
        }
    }

    public function getAuthUser(Request $request)
    {
        $this->validate($request, [
            'token' => 'required'
        ]);

        $user = JWTAuth::authenticate($request->token);

        return response()->json(['user' => $user]);
    }
}
```
代码流程讲解：
-  在 register 方法中，我们接收了 RegisterAuthRequest 。使用请求中的数据创建用户。如果 loginAfterSignUp 属性为 true ，则注册后通过调用 login 方法为用户登录。否则，成功的响应则将伴随用户数据一起返回。
- 在 login 方法中，我们得到了请求的子集，其中只包含电子邮件和密码。以输入的值作为参数调用  JWTAuth::attempt() ，响应保存在一个变量中。如果从 attempt 方法中返回 false ，则返回一个失败响应。否则，将返回一个成功的响应。
- 在 logout 方法中，验证请求是否包含令牌验证。通过调用 invalidate 方法使令牌无效，并返回一个成功的响应。如果捕获到 JWTException 异常，则返回一个失败的响应。
- 在 getAuthUser 方法中，验证请求是否包含令牌字段。然后调用 authenticate 方法，该方法返回经过身份验证的用户。最后，返回带有用户的响应。


#### JWT核心源码追踪

```
//JWTAuth.php
    public function attempt(array $credentials)
    {
        if (! $this->auth->byCredentials($credentials)) {
            return false;
        }

        return $this->fromUser($this->user());
    }
//EloquentUserProvider.php
    public function retrieveByCredentials(array $credentials)
    {
        if (empty($credentials) ||
           (count($credentials) === 1 &&
            array_key_exists('password', $credentials))) {
            return;
        }

        // First we will add each credential element to the query as a where clause.
        // Then we can execute the query and, if we found a user, return it in a
        // Eloquent User "model" that will be utilized by the Guard instances.
        $query = $this->createModel()->newQuery();
        foreach ($credentials as $key => $value) {
            if (Str::contains($key, 'password')) {
                continue;
            }

            if (is_array($value) || $value instanceof Arrayable) {
                $query->whereIn($key, $value);
            } else {
                $query->where($key, $value);
            }
        }
        return $query->first();
    }

    /**
     * Validate a user against the given credentials.
     *
     * @param  \Illuminate\Contracts\Auth\Authenticatable  $user
     * @param  array  $credentials
     * @return bool
     */
    public function validateCredentials(UserContract $user, array $credentials)
    {
        $plain = $credentials['password'];
        return $this->hasher->check($plain, $user->getAuthPassword());
    }
//HashManager.php
    public function check($value, $hashedValue, array $options = [])
    {
        return $this->driver()->check($value, $hashedValue, $options);
    }
//AbstractHasher
    public function check($value, $hashedValue, array $options = [])
    {
        if (strlen($hashedValue) === 0) {
            return false;
        }
        return password_verify($value, $hashedValue);
    }
```

#### 版本科普

- α（Alpha）版
​    这个版本表示该 Package 仅仅是一个初步完成品，通常只在开发者内部交流，也有很少一部分发布给专业测试人员。一般而言，该版本软件的 Bug 较多，普通用户最好不要安装。


- β（Beta）版
该版本相对于 α（Alpha）版已有了很大的改进，修复了严重的错误，但还是存在着一些缺陷，需要经过大规模的发布测试来进一步消除。通过一些专业爱好者的测试，将结果反馈给开发者，开发者们再进行有针对性的修改。该版本也不适合一般用户安装。


- RC/ Preview版
RC 即 Release Candidate 的缩写，作为一个固定术语，意味着最终版本准备就绪。一般来说 RC 版本已经完成全部功能并清除大部分的 BUG。一般到了这个阶段 Package 的作者只会修复 Bug，不会对软件做任何大的更改。


- 普通发行版本
一般在经历了上面三个版本后，作者会推出此版本。此版本修复了绝大部分的 Bug，并且会维护一定的时间。（时间根据作者的意愿而决定，例如 Laravel 的一般发行版本会提供为期一年的维护支持。）


- LTS（Long Term Support） 版
该版本是一个特殊的版本，和普通版本旨在支持比正常时间更长的时间。（例如 Laravel 的 LTS 版本会提供为期三年的 维护支持。）
