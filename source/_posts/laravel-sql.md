---
title: Laravel常用数据库操作
categories: 
- PHP
---

**一、insert**
1、插入单条：
`DB::table('users')->insert(['name' => 'test', 'age' => 18])`
<!--more-->
2、插入多条：

```
DB::table('users')->insert([
     ['name' => 'test', 'age' => 18],
    ['name' => 'test2', 'age' => 20],
]);
```

**二、update**
`DB::table('users')->where('id', 1)->update(['name'=>'test2']);`

**三、delect**
`DB::table('users')->where('id', 1)->delete();`

**四、select**

1、查询所有：
`DB::table('users')->get();`

2、查询单行：
`DB::table('users')->where('id', 1)->first();`

3、按字段查询：
`DB::table('users')->lists('title', 'name');`

4、where 条件：
`DB::table('users')->where('age', '>', 10)->get();`
`DB::table('users')->where('age', '>', 10)->orWhere('name', 'test')->get();`

```
DB::table('users')
->where('age', '=', '20')
->orWhere(function($query) {
    $query->where('sex', '=', 'man')
})
->get();

以上例子将执行如下sql:
select * from users where age= '20' or (sex = 'man')
```

5、where Between

```
DB::table('users')->whereBetween('age', [1, 100])->get();
DB::table('users')->whereNotBetween('age', [10, 20])->get();
```

6、Where In

```
DB::table('users')->whereIn('age', [10, 12, 13])->get();
DB::table('users')->whereNotIn('age',  [10, 12, 13])->get();
```

7、where Null
`DB::table('users')->whereNull('date')->get();`

8、Order By, Group By, And Having

`DB::table('users')->orderBy('id', 'desc')->groupBy('age')->having('age', '>', 20)->get();`

**五、关联查询**
1、join

```
DB::table('users')
　　->join('orders', 'users.id', '=', 'orders.user_id')
　　->select('users.name', 'orders.price')
　　->get();
```

2、leftJoin
`DB::table('users')->leftJoin('orders', 'users.id', '=', 'orders.user_id')->get();`

**六、执行原生 sql：**

```
DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);
DB::update('update users set votes = 100 where name = ?', ['John']);
DB::delete('delete from users');
DB::select('select * from users where active = ?', [1])
```

**七、事物**

```
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);

    DB::table('posts')->delete();
});
```

**八、处理死锁**

transaction() 可接收第二参数，为事物发生死锁时重试的次数

```
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);

    DB::table('posts')->delete();
}, 5);
```

---
