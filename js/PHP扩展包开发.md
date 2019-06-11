# PHP扩展包开发

### Composer的基本使用

。。。



### 扩展包的基础结构

这一块省略掉。详情参考这篇教程 [扩展包基础结构](https://learnku.com/courses/creating-package/expanding-the-infrastructure-of-the-package/2069)



实际开发中，推荐用超哥的包构建工具 [overtrue/package-builder](https://github.com/overtrue/package-builder)

安装方法：

> `composer global require "overtrue/package-builder" --prefer-source`

基本用法：

> `package-builder build [目标目录]`



### 创建项目+异常处理+单元测试

省略写扩展包代码的部分。。。。。。



### 测试扩展包

逻辑与单元测试都写好了，还需要测试包是否可用

1. 创建测试项目

   比如：`mkdir package-test` 然后 `cd package-test`

2. 然后在这个测试项目根目录中使用composer引入包：

   ```
   # 需要先初始化 composer.json, 一路回车即可
   $ composer init  
   
   # 配置包路径，注意，这里 `../package-test` 为相对路径，不要弄错了
   $ composer config repositories.package-test path ../package-test    
   
   # 安装扩展包  这里  `dev-master`  中的 dev 指该分支下最新的提交，master 是指定的包中的分支名
   $ composer require tdtzzz/package-test:dev-master
   
   ```

3. 上面操作需要注意的是 `composer config`,它会在`composer.json`中添加如下部分:

   ```
   .
   .
   .
        "repositories": {
           "weather": {
               "type": "path",
               "url": "../package-test"
           }
         }
   .
   .
   .
   ```

   这个操作会在我们安装的时候，composer会建立一个**软链接**，`vendor/tdtzzz/package-test`到包所在目录`../package-test`。

   这样的话，直接在`vendor`下修改文件，包里面也会随之修改，开发起来很方便。





### Laravel扩展包开发

[参考文章](https://learnku.com/articles/7426/how-to-develop-test-and-publish-a-laravel-extension-package)

掌握laravel扩展包前最好先理解Laravel的IOC，服务提供者等基础概念。

和上述的PHP扩展包开发比较起来，laravel下的开发有两点区别。

1. 服务提供者(ServiceProvider)：

   需要在`src`目录下新建个`Provider`文件，参考代码如下：

   ```php
   <?php
   
   namespace Tdtzzz\Weather;
   
   class ServiceProvider extends \Illuminate\Support\ServiceProvider
   {
       protected $defer = true;
   
       public function register()
       {
           $this->app->singleton(Weather::class, function(){
               return new Weather(config('services.weather.key'));
           });
   
           $this->app->alias(Weather::class, 'weather');
       }
   
       public function provides()
       {
           return [Weather::class, 'weather'];
       }
   }
   ```

2. 配置Laravel Auto Discovery

   Laravel5.5后支持包自动发现。无需像以前那样手动在`config/app.php`里填写服务提供者。只需在composer.json里加入如下配置：

   ```php
   .
   .
   .
   "extra": {
       "laravel": {
           "providers": [
               "Tdtzzz\\Weather\\ServiceProvider"
           ]
       }
   }
   ```

   **注意：这个composer.json是该扩展包的，不是laravel项目的compose.json。**



### 