# 18 Generator 函数的语法

学习时间：45min (未完待续)

## 知识点汇总

1. Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。Generator 函数是一个状态机，封装了多个内部状态，它也是一个遍历器对象生成函数。执行 Generator 函数会返回一个**遍历器对象**，可以依次遍历 Generator 函数内部的每一个状态。

2. 形式上，Generator 函数是一个普通函数，但是有两个特征。一是，`function`关键字与函数名之间有一个星号；二是，函数体内部使用`yield`表达式，定义不同的内部状态（`yield`在英语里的意思就是“产出”）。

   ```js
   function* helloWorldGenerator() {
     yield 'hello';
     yield 'world';
     return 'ending';
   }
   // 一般Generator函数的写法星号紧跟在function关键字后面。
   var hw = helloWorldGenerator();
   hw.next()
   // { value: 'hello', done: false }
   
   hw.next()
   // { value: 'world', done: false }
   
   hw.next()
   // { value: 'ending', done: true }
   
   hw.next()
   // { value: undefined, done: true }
   ```

3. 调用 Generator 函数后，该函数并不执行，返回的也不是函数运行结果，而是一个指向内部状态的指针对象，也就是上一章介绍的遍历器对象（Iterator Object）。

4. 调用遍历器对象的`next`方法，使得指针移向下一个状态。每次调用`next`方法，内部指针就从函数头部或上一次停下来的地方开始执行，直到遇到下一个`yield`表达式（或`return`语句）为止。**`yield`表达式是暂停执行的标记，而`next`方法可以恢复执行。**

5. 每次调用遍历器对象的`next`方法，就会返回一个有着`value`和`done`两个属性的对象。`value`属性表示当前的内部状态的值，是`yield`表达式后面那个表达式的值；`done`属性是一个布尔值，表示是否遍历结束。

6. `yield`表达式后面的表达式，只有当调用`next`方法、内部指针指向该语句时才会执行，因此等于为 JavaScript 提供了手动的“惰性求值”（Lazy Evaluation）的语法功能。

   ```js
   function* gen() {
     yield  123 + 456;
   }
   // yield后面的表达式123 + 456，不会立即求值，只会在next方法将指针移到这一句时，才会求值。
   ```

7. `yield`表达式与`return`语句既有异同。同：都能返回紧跟在语句后面的那个表达式的值。异：`return`语句不具备位置记忆的功能。

8. Generator 函数可以不用`yield`表达式，这时就变成了一个单纯的暂缓执行函数。`yield`表达式只能用在 Generator 函数里面，用在其他地方都会报错。

9. `yield`表达式如果用在另一个表达式之中，必须放在圆括号里面。

   ```js
   function* demo() {
     console.log('Hello' + (yield)); // OK
     console.log('Hello' + (yield 123)); // OK
   }
   ```

10. Generator 函数就是遍历器生成函数，因此可以把 Generator 赋值给对象的`Symbol.iterator`属性，从而使得该对象具有 Iterator 接口。

    ```js
    var myIterable = {};
    myIterable[Symbol.iterator] = function* () {
      yield 1;
      yield 2;
      yield 3;
    };
    
    [...myIterable] // [1, 2, 3]
    ```











## 疑惑

1. 理解一下这段代码

   ```js
   function* gen(){
     // some code
   }
   
   var g = gen();
   
   g[Symbol.iterator]() === g
   // true
   // gen是一个 Generator 函数，调用它会生成一个遍历器对象g。它的Symbol.iterator属性，也是一个遍历器对象生成函数，执行后返回它自己。
   ```

   