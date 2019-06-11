# ES6



## promise



then catch finally all

1.catch冒泡机制，promise的error具有冒泡性质，直到被捕捉(catch)









## Generator

### yield



> genenrator可以不用yield，当成一个单纯的暂缓执行函数

```javascript
function* f() {
  console.log('执行了！')
}

var generator = f();

setTimeout(function () {
  //不next就不执行
  generator.next()
}, 2000);
```



1. yield如果放在其他表达式之中，需要放在括号内

   ```javascript
   function* demo() {
       console.log('11' + (yield))
       console.log('22' + (yield 123))
   }
   
   let demoG = demo()
   
   console.log(demoG.next())
   console.log(demoG.next())
   ```

   

2. yield表达式用作函数参数或放在赋值表达式的右边，可以不加括号

   ```javascript
   function* demo() {
     foo(yield 'a', yield 'b'); // OK
     let input = yield; // OK
   }
   ```

3. Genetrator.prototype. next+throw+return

> `next()`、`throw()`、`return()`这三个方法本质上是同一件事，可以放在一起理解。它们的作用都是让 Generator 函数





## Async

> generator函数的语法糖，但有如下四点改进:

1. Generator 函数的执行必须靠执行器，所以才有了`co`模块，而`async`函数自带执行器。也就是说，`async`函数的执行，与普通函数一模一样，只要一行。
2. 更好的语义  async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。
3. 更广的适用性  `co`模块约定，`yield`命令后面只能是 Thunk 函数或 Promise 对象，而`async`函数的`await`命令后面，可以是 Promise 对象和原始类型的值（数值、字符串和布尔值，但这时会自动转成立即 resolved 的 Promise 对象）
4. 返回值是Promise  `async`函数的返回值是 Promise 对象，这比 Generator 函数的返回值是 Iterator 对象方便多了。你可以用`then`方法指定下一步的操作。



* 返回Promise
* Promise状态的改变



有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个`await`放在`try...catch`结构里面，这样不管这个异步操作是否成功，第二个`await`都会执行。

```javascript
async function f() {
  try {
    await Promise.reject('出错了');
  } catch(e) {
  }
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// hello world
```

另一种方法是`await`后面的 Promise 对象再跟一个`catch`方法，处理前面可能出现的错误。

```javascript
async function f() {
  await Promise.reject('出错了')
    .catch(e => console.log(e));
  return await Promise.resolve('hello world');
}

f()
.then(v => console.log(v))
// 出错了
// hello world
```

注意，如果不处理Promise.reject的话，那么忧郁Promise的状态改变，就不会往下执行了

```javascript
async function f(){
	await Promise.reject('出错了')
  return await Promise.resolve('hello world');
}

f().then(v => {
    console.log(v)
}).catch(e => {
    console.log(e)
})
```



* 错误处理

  > 若果`await`后面的异步操作被出错，那么等同于`async`函数返回的`Promise`对象被`reject`

```javascript
async function f() {
  await new Promise(function (resolve, reject) {
    throw new Error('出错了');
  });
}

f()
.then(v => console.log(v))
.catch(e => console.log(e))
// Error：出错了
```

> 防止出错的方法，也就是将其放在`try…catch`代码块之中

```javascript
async function f() {
  try {
    await new Promise(function (resolve, reject) {
      throw new Error('出错了');
    });
  } catch(e) {
  }
  return await('hello world');
}
```

下面的例子使用`try…catch`结构，实现多次重复尝试

```javascript
const superagent = require('superagent');
const NUM_RETRIES = 3;

async function test() {
  let i;
  for (i = 0; i < NUM_RETRIES; ++i) {
    try {
      await superagent.get('http://google.com/this-throws-an-error');
      break;
    } catch(err) {}
  }
  console.log(i); // 3
}

test();
```

上面代码中，如果`await`操作成功，就会使用`break`语句退出循环；如果失败，会被`catch`语句捕捉，然后进入下一轮循环。



### 使用注意点

---

1. `await`命令后面的`Promise`对象，运行结果可能是`rejected`,所以最好把`await`命令放在`try…catch`代码块中

   ```javascript
   async function myFunction() {
     try {
       await somethingThatReturnsAPromise();
     } catch (err) {
       console.log(err);
     }
   }
   
   // 另一种写法
   
   async function myFunction() {
     await somethingThatReturnsAPromise()
     .catch(function (err) {
       console.log(err);
     });
   }
   ```

2. 多个`await`命令后面的异步操作，如果不存在继发关系，最好让他们同时触发。

```javascript
let foo = await getFoo();
let bar = await getBar();
```

多个`await`命令后面的异步操作，如果不存在继发关系，最好让它们同时触发。

```javascript
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()]);

// 写法二
let fooPromise = getFoo();
let barPromise = getBar();
let foo = await fooPromise;
let bar = await barPromise;
```

上面两种写法，`getFoo`和`getBar`都能同时触发，这样就会缩短程序的执行时间。



3. `await`只能用在`async`函数之中，如果用在普通函数里，就会报错

   > 如果确实希望多个请求并发执行，可以使用`Promise.all`方法。当三个请求都会`resolved`时，下面两种写法效果相同。

   ```javascript
   async function dbFuc(db) {
     let docs = [{}, {}, {}];
     let promises = docs.map((doc) => db.post(doc));
   
     let results = await Promise.all(promises);
     console.log(results);
   }
   
   // 或者使用下面的写法
   
   async function dbFuc(db) {
     let docs = [{}, {}, {}];
     let promises = docs.map((doc) => db.post(doc));
   
     let results = [];
     for (let promise of promises) {
       results.push(await promise);
     }
     console.log(results);
   }
   ```



4. async函数的实现原理



5. 与其他异步处理方法的比较

   Promise

   Genetator











---

## Class Basic



> `Class`的数据类型就是`function`

```javascript
class Point {
  // ...
}

typeof Point // "function"
Point === Point.prototype.constructor // true
```



> 构造函数的`prototype`属性，在ES6的`Class`上面继续存在。事实上，类的所有方法都定义在类的`prototype`属性上面

```javascript
class Point {
  constructor() {
    // ...
  }

  toString() {
    // ...
  }

  toValue() {
    // ...
  }
}

// 等同于

Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};

```



---

### 取值函数(getter)和存值函数(setter)

```javascript
class MyClass {
  constructor() {
    // ...
  }
  get prop() {
    return 'getter';
  }
  set prop(value) {
    console.log('setter: '+value);
  }
}

let inst = new MyClass();

inst.prop = 123;
// setter: 123

inst.prop
// 'getter'
```



* **属性表达式**

  类的属性名，可以采用表达式

  ```javascript
  let methodName = 'getArea';
  
  class Square {
    constructor(length) {
      // ...
    }
  
    [methodName]() {
      // ...
    }
  }
  ```

  上面代码中，`Square`类的方法名`getArea`，是从表达式得到的。

* **Class表达式**

* **静态属性**

  > 静态属性是指`Class`本身的属性，即`Class.propName`,而不是定义在实例对象(`this`)上的属性。

  ```javascript
  // 老写法
  class Foo {
    // ...
  }
  Foo.prop = 1;
  
  // 新写法
  class Foo {
    static prop = 1;
  }
  ```



### Class的继承

