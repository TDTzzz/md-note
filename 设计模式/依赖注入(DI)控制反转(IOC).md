# 依赖注入(DI)/控制反转(IOC)

> 首先谈谈什么叫依赖，依赖 就是不是自身的 但要创造自身时，必须得有它才行。如下代码：

```php
class Bread{
    
}

class Bacon{
    
}

class Hamburger{
    protected $materials;
 	//这里可以看到，一个汉堡类的创建，必须依赖于Bread类和Bacon类   
    public function __construct(Bread $bread,Bacon $bacon){
        $this->materials=[$bread,$bacon];
    }
}

class Cola{
    
}

class Meal{
    protected $food;
    protected $drink;
    //一个Meal类依赖于一个 Cola 和 Hamburger
    public function __construct(Hamburger $hamburger,Cola $cola){
        $this->food=$hamburger;
        $this->drink=$cola;
    }
}
```



> 那么想一想，要想new一个Hamburger的话。是不是得先把 bread和bacon给new出来。
>
> 这样的话，耦合性很高，实例一个有多依赖的类会很麻烦。。。。。。
>
> 为了解决这个问题，**容器**就应运而生了，容器的定位就是“管理类的依赖和执行依赖注入额工具”。
>
> 通过容器，我们可以将实例化的这个过程自动化，然后用一行代码获取 一个多依赖的类(Hamburger)



```php
class Container{
    protected $binds=[];
    
    //bind方法里绑定那些 需要依赖的类。
    //绑定的时候传入的闭包函数，用来产生所需的依赖，以便返回绑定的类。
    public function bind(string $class,Closure $closure){
        $this->binds[$class]=$closure;
        return $this;
    }
    
    public function make(string $class,array $params=[]){
        //这个地方要酝酿一下。检测到make的class在容器的binds里。
        //说明这个class是需要依赖的，之前绑定过
        //那么就用call执行binds里的Closure，自动生成bind的class。
        if(isset($this->binds[$class])){
            return ($this->binds[$class])->call($this,$this,...$params);
        }
        
        return new $class(...$params);
    }
}
```



> 生成操作如下



```php
$container = new Container();

$container->bind(Hamburger::class, function (Container $container) {
    $bread = $container->make(Bread::class);
    $bacon = $container->make(Bacon::class);

    return new Hamburger($bread, $bacon);
});

$container->bind(Meal::class, function (Container $container) {
    $hamburger = $container->make(Hamburger::class);
    $cola      = $container->make(Cola::class);
    return new Meal($hamburger, $cola);
});

// 输出 Meal
echo get_class($container->make(Meal::class));
```



> 上面的思路其实还是比较麻烦，因为还是要将所需要的依赖 bind 进去。思考一下，可以自动将所需的依赖生成，无需手动bind吗。。。
>
> 利用 反射--Refection 即可实现

```php
public function make(string $class,$params){
    if(isset($this->binds[$class])){
        return ($this->binds[$class])->call($this,$this,...$params)
    }
    //注意：这里和之前不一样
    return $this->resolve($class);
}

public function resolve(string $class){
    //先获得构造函数
    $constructor=(new ReflectionClass($class))->getConstructor();
    //构造函数未定义的话就直接返回
    if(is_null($constructor)){
        return new $class;
    }
    //获得构造函数的参数
    $parameters=$constructor->getParameters();
    $arguments=[];
    
    foreach($parameters as $parameter){
        //获得参数的类型提示类
        $paramClassName=$parameter->getClass()->name;
        //参数没有类型提示类，则报错
        if(is_null($paramClassName)){
            throw new Exception("Fail to get instance by reflection");
        }
        //实例化参数
        $arguments[]=$this->make($paramClassName);
    }
    
    return new $class(...$arguments);
}

```



> 然后就可以这样 创建 多依赖的类了 **$container->make(Meal::class);**

