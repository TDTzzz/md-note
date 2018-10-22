# Redis

[参考文章](https://blog.csdn.net/Dennis_ukagaka/article/details/78072274)

### redis加锁命令分别是1.INCR   2.SETNX   3.SET



### 1.INCR







### 感受下这段代码



```php
        do {  //针对问题1，使用循环
            $timeout = 10;
            $roomid = 10001;
            $key = 'room_lock';
            $value = 'room_'.$roomid;  //分配一个随机的值针对问题3
            $isLock = Redis::set($key, $value, 'ex', $timeout, 'nx');//ex 秒
            if ($isLock) {
                if (Redis::get($key) == $value) {  //防止提前过期，误删其它请求创建的锁
                    //执行内部代码
                    Redis::del($key);
                    continue;//执行成功删除key并跳出循环
                }
            } else {
                usleep(5000); //睡眠，降低抢锁频率，缓解redis压力，针对问题2
            }
        } while(!$isLock);

```

