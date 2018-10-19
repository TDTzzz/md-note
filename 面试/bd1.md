记录百度一面
=========

### 1.自我介绍



### 2.php的错误级别



### 手写单例模式

```php
class Singleton{
    //静态成品变量，保存全局实例
    private static $_instance=null;
    
    //私有化默认方法，保证外部变量无法直接实例化
    private function __construct(){
        
    }
    
    //静态工厂方法，返回此类的唯一实例
    public static function s(){
        if(is_null(self::$instance)){
            self::$_instance=new Singleton();
        }
        
        return self::$instance;
    }
    
    //防止用户克隆
    public function __clone(){
        die("Clone is not allowed").E_USER_ERROR;
    }
    
   /**
   * 测试用方法
   */
   public function test()
   {
      echo 'Singleton Test OK!';
   }
}


```



### echo和printf的区别

* echo是回显。即代表回车显示，自带换行的；而printf只是打印出来，没有换行



### 手写常用的字符串 数组函数



### 手写一个简单的sql（一个app_id对应多个ac_id,要求获取该app_id的前三个活动以及总共的活动数）



### session cookie的区别



### include require的区别,require在一次请求里会加载几次，哪个更快

---

* include和require除了处理错误的方式不同外，其他方面都是相同的
* require声明一个致命错误（E_COMPILE_ERROR），在错误发生后脚本会停止执行
* Include生成一个警告(E_WARNING),在错误发生后会继续执行
* 





### 二叉树中序排列



### 手写一个算法，有序数组比如 12233344566777，输入数组和key，返回该key的个数



### redis的数据类型以及常用的一些操作



### tcp三次握手



### php的全称



### http状态码



### crontab的写法



### linux的日常命令





### 正则表达式



### debug方法



### laravel的路由



### 大型网站常见优化技巧



### 扯redis消息队列时，我扯到了rabbitmq。。。然后似乎很感兴趣，xjb扯了扯





## 









