# php的trait

> 自PHP5.4起，PHP实现了一种代码复用的方法，称之为trait

>用于实现多继承的类似操作

Trait 是为类似 PHP 的**单继承语言**而准备的一种**代码复用机制**。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。

Trait 和 Class 相似，但仅仅旨在用细粒度和一致的方式来组合功能。 无法通过 trait 自身来实例化。它为传统继承增加了水平特性的组合；也就是说，应用的几个 Class 之间不需要继承。



## 优先级

从基类继承的成员会被 trait 插入的成员所覆盖。优先顺序是来自当前类的成员覆盖了 trait 的方法，而 trait 则覆盖了被继承的方法。

> 当前类的func > trait的func > 其他被继承的func





## 多个trait

> 通过逗号分隔，在 use 声明列出多个 trait，可以都插入到一个类中。

```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World';
    }
}

class MyHelloWorld {
    use Hello, World;
    public function sayExclamationMark() {
        echo '!';
    }
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
$o->sayExclamationMark();
```





## 解决冲突



如果两个 trait 都插入了一个同名的方法，如果没有明确解决冲突将会产生一个致命错误。

为了解决多个 trait 在同一个类中的命名冲突，需要使用 *insteadof* 操作符来明确指定使用冲突方法中的哪一个。

以上方式仅允许排除掉其它方法，*as* 操作符可以 为某个方法引入别名。 注意，*as* 操作符不会对方法进行重命名，也不会影响其方法。



```php
<?php

trait A
{
    public function smallTalk()
    {
        echo 'a';
    }

    public function bigTalk()
    {
        echo 'A';
    }
}

trait B
{
    public function smallTalk()
    {
        echo 'b';
    }

    public function bigTalk()
    {
        echo 'B';
    }
}

class Talker
{
    use A, B {
        //这里是解决冲突，代表用trait B中的smallTalk，不用A的
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
    }
}

class Aliased_Talker
{
    use A, B {
        B::smallTalk insteadof A;
        A::bigTalk insteadof B;
        //as是定义别名，下面的写法可以用 talk 调用B的bigTalk。
        B::bigTalk as talk;
    }
}


```



### 修改权限控制

> 使用 *as* 语法还可以用来调整方法的访问控制。

```php
<?php
trait HelloWorld {
    public function sayHello() {
        echo 'Hello World!';
    }
}

// 修改 sayHello 的访问控制
class MyClass1 {
    use HelloWorld { sayHello as protected; }
}

// 给方法一个改变了访问控制的别名
// 原版 sayHello 的访问控制则没有发生变化
class MyClass2 {
    use HelloWorld { sayHello as private myPrivateHello; }

    public function __construct()
    {
        //注意上面的myPrivateHello是private。只能在class内部使用
        $this->myPrivateHello();
    }
}
//myPrivateHello被修改成了private，不代表sayHello被修改了。
//所以 外部调用可以调public的sayHello，不能private的myPrivateHello
(new MyClass2())->sayHello();

```



## 从trait来的trait

正如 class 能够使用 trait 一样，其它 trait 也能够使用 trait。在 trait 定义时通过使用一个或多个 trait，能够组合其它 trait 中的部分或全部成员。



```php
<?php
trait Hello {
    public function sayHello() {
        echo 'Hello ';
    }
}

trait World {
    public function sayWorld() {
        echo 'World!';
    }
}

trait HelloWorld {
    use Hello, World;
}

class MyHelloWorld {
    use HelloWorld;
}

$o = new MyHelloWorld();
$o->sayHello();
$o->sayWorld();
```



## trait同样支持 抽象成员 静态成员 属性

> 注意：trait 定义了一个属性后，类就不能定义同样名称的属性，否则会产生 fatal error。 有种情况例外：属性是兼容的（同样的访问可见度、初始默认值）。 在 PHP 7.0 之前，属性是兼容的，则会有 E_STRICT 的提醒。