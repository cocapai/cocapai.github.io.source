---
title: laravel的错误归档
date: 2019-10-10 11:04:19
tags: [laravel]
---

【laravel5.6】 Illuminate\Database\QueryException : SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; max key length is 1000 bytes

在进行数据迁移时候报错： 特殊字段太长报错，
```
php artisan migrate
```
现在utf8mb4包括存储emojis支持。如果你运行MySQL v5.7.7或者更高版本，则不需要做任何事情。

当你试着在一些MariaDB或者一些老版本的的MySQL上运行 migrations 命令时，你可能会碰到下面这个错误：

错误如下：

  Illuminate\Database\QueryException  : SQLSTATE[42000]: Syntax error or access violation: 1071 Specified key was too long; max key length is 1000 bytes

解决办法： 

我们可以在 AppServiceProvider.php 文件里的 boot 方法里设置一个默认值：

文件路径： /app/providers/AppServiceProvider.php，加上绿色部分即可
```
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Schema;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        //
        Schema::defaultStringLength(191);
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```