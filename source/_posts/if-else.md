---
title: 聊聊代码中的if else
categories: 
- 优化
---
我们平时写代码的时候会写很多if else，但是随着业务逻辑的增加并且代码是多人维护的情况下，满屏的if else会让人很绝望。回头想想 if else 无非两种场景: **异常逻辑处理和不同状态处理**。两者最主要的区别是：异常逻辑处理说明只能一个分支是正常流程，而不同状态处理都所有分支都是正常流程。
<!--more-->
最近在网上看了一些帖子，结合实际自己写代码的经验总结了以下几点，希望对大家有所帮助

#### 1. 提前return，去除不必要的else
维持正常流程代码在最外层
**优化前：**
```
if($condition){
    //doSomething
}else{
    return ;
}
```
**优化后：**
```
if（!$condition）{
    return ;
}
//doSomething
```

#### 2. 使用条件三目运算符
使用三目运算符，是代码行数更少，更容易阅读
**优化前：**
```
if($condition){
   return $price = 80;
}else{
   return $price = 100;
}
```
**优化后：**
```
$price = $condition ? 80 : 100;
```

#### 3. 合并条件表达式
如果一系列if返回值是一样的，就可以把这些条件合并起来，让逻辑更加清晰,
**优化前：**
```
function getResult(){
    if($a < 100){
        return true;
    }
    if($b > 200){
        return true;
    }
    if($c >= 300){
        return true;
    }
}
```
**优化后：**
```
function getResult(){
    if($a < 100){
        return true;
    }
    if($b <= 200 || $c >= 300){
        return false;
    }
    //do someting
}
```

#### 4. 优化逻辑结构，让正常流程走主干
将if条件取反，让正常逻辑走主干
**优化前：**
```
function getResult(){
    if($a < 100){
        return true;
    }
    if($b > 200 && $c <300){
        //do something
    }
    return false;
}
```
**优化后：**
```
function getResult(){
    if($a < 100){
        return true;
    }
    if($b <= 200 || $c >= 300){
        return false;
    }
    //do someting
}
```


#### 5. 策略模式+工厂方法消除if else
假设需求为，根据不同勋章类型，处理相对应的勋章服务，优化前有以下代码：
```
if($title == 'guest'){
    return $role = "嘉宾";
}else if ($title == "VIP"){
    return $role "会员";
 }else if($title == "admin"){
    return $role "管理员";
 }
```
**优化步骤**
首先，定义一个接口类
```
public interface showRole{
        public function getRoleName();
}
```
根据业务逻辑，实现相应的策略类

```
public class guest implements showRole{
    public getRoleName(){
        return "嘉宾";
    }
}

public class VIP implements showRole{
    public getRoleName(){
        return "会员";
    }
}

public class admin implements showRole{
    public getRoleName(){
        return "管理员";
    }
}
```
接下来在定义一个工厂类，来实例化策略类
```
public function showRoleFactory(){
        public static $Obj;
        
        public static function getShowRole($title)
        switch($title)
        case 'guest':
            self::$Obj = new guest();
            break;
        case 'VIP'
            self::$Obj = new VIP():
            break;
        case 'admin'
            self::$Obj = new admin();
            break;
}
```
使用工厂类
```
    $obj = showRoleFactory::getShowRole('guest');
    $obj->getRoleName();
```


##### 参考与感谢
>[if-else代码优化的八种方案](
https://juejin.im/post/5e5fa79de51d45271849e7bd?utm_source=gold_browser_extension)
>[6个实例详解如何把if-else代码重构成高质量代码](
https://blog.csdn.net/qq_35440678/article/details/77939999)
>[如何 “干掉” if...else](
https://www.jianshu.com/p/1db0bba283f0)

