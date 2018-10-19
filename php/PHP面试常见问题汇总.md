# PHP面试常见问题汇总

### echo print_r print  var_dump的区别

1. echo、print是php语句，var_dump和print_r是函数

2. echo 输出一个或多个字符串**，**中间以逗号隔开，没有返回值是语言结构而不是真正的函数，因此不能作为表达式的一部分使用

   ```php
   echo 'a','b','c';
   //输出：abc
   ```

3. print也是php的一个关键字，有返回值 只能打印出简单类型变量的值(如int，string)，如果字符串显示成功则返回true，否则返回false。

   print只能输出一个字符串。

   ```php
   print 'a','b','c';  //错误
   print 'abc';        //正确
   print 'a'.'b'.'c';  //正确
   ```

   > echo无返回值，print有返回值1.
   >
   > 因此:
   >
   > ```php
   > $ret = print 'abc';
   > echo $ret + 1;
   > //输出abc2
   > ```
   >
   > echo 速度比print快


4. **print_r** 可以打印出复杂类型变量的值(如数组、对象）以列表的形式显示，并以array、object开头。

   `print_r()` 显示关于一个变量的易于理解的信息。如果给出的是 string、integer 或 float，将打印变量值本身。如果给出的是 array，将会按照一定格式显示键和元素。object 与数组类似。

   `print_r()` 会舍弃掉小数位末尾的 “0”；布尔值 true 输出 1，false 不输出；空字符串 和 null 不输出。如果给出的是 array，将会按照一定格式显示键和元素。object 与数组类似。

5. **var_dump()** 判断一个变量的类型和长度，并输出变量的数值，一般调试用这个。



### PHP7和PHP5的区别，具体多了哪些新特性

[官方文档--新特性](http://php.net/manual/zh/migration70.new-features.php)

1. 速度提升很多（可以从底层讲起，鸟哥博客里有文章详细讲到--zval改变）

2. 新增 太空船操作符 <=>

3. 新增 NULL合并运算符

4. php标量类型和返回类型声明

5. 常量数组

6. 新增匿名类，支持通过new class实例化一个匿名类，这可以用来替代一些“用后即焚”的完整类定义

   ```php
   <?php
   
   // PHP 7 之前的代码
   class Logger
   {
       public function log($msg)
       {
           echo $msg;
       }
   }
   
   $util->setLogger(new Logger());
   
   // 使用了 PHP 7+ 后的代码
   $util->setLogger(new class {
       public function log($msg)
       {
           echo $msg;
       }
   });
   ```

   > 匿名类的好处在于创建一次性的简单对象，即“阅后即焚”。

7. try catch增加多条件判断，更多的error错误可以进行异常处理

   。。。。。。



### define和const的区别



### PHP的垃圾回收机制（GC）

> PHP 5.3 版本之前都是采用引用计数的方式管理内存，PHP 所有的变量存在一个叫 `zval` 的变量容器中，当变量被引用的时候，引用计数会+1，变量引用计数变为0时，PHP 将在内存中销毁这个变量。
>
> 但是引用计数中的循环引用，引用计数不会消减为 0，就会导致内存泄露。
>
> 在 5.3 版本之后，做了这些优化：
>
> 1. 并不是每次引用计数减少时都进入回收周期，只有根缓冲区满额后在开始垃圾回收；
> 2. 可以解决循环引用问题；
> 3. 可以总将内存泄露保持在一个阈值以下。



### 如何解决PHP内存溢出的问题

> 1. 增大 PHP 脚本的内存分配
> 2. 变量引用之后及时销毁
> 3. 将数据分批处理