# 9 数组的扩展

学习时间:  240min

## 知识点汇总

1. 扩展运算符（spread）是三个点（`…`），将一个数组转为用逗号分隔的参数序列。

   ```js
   console.log(...[1, 2, 3])
   // 1 2 3
   ```

2. 扩展运算符与正常的函数参数可以结合使用。扩展运算符后面还可以放置表达式。如果扩展运算符后面是一个空数组，则不产生任何效果。

   ```js
   [...[], 1]
   // [1]
   ```

3. 只有函数调用时，扩展运算符才可以放在圆括号中，否则会报错。

4. 由于扩展运算符可以展开数组，所以不再需要`apply`方法，将数组转为函数的参数了。

   ```js
   // ES5 的写法
   function f(x, y, z) {
     // ...
   }
   var args = [0, 1, 2];
   f.apply(null, args);
   // 为什么这里写null？？？
   
   // ES6的写法
   function f(x, y, z) {
     // ...
   }
   let args = [0, 1, 2];
   f(...args);
   
   // ES5
   // 为什么这里有个null？？？
   new (Date.bind.apply(Date, [null, 2015, 1, 1]))
   // ES6
   new Date(...[2015, 1, 1]);
   ```

5. **扩展运算符的运用**

- 复制数组

  ```js
  const a = [1,2,3]
  const b = [...a]
  // or
  const [...b] = a
  // 这种写法要怎么理解？？？解构赋值吗？？？
  ```

- 合并数组(浅拷贝)

  ```js
  const a = [1,2,3]
  const b = [4,5,6]
  const c = [...a, ...b]
  // [1,2,3,4,5,6]
  ```

- 与解构赋值结合

  ```js
  const [first, ...rest] = [1, 2, 3, 4, 5];
  first // 1
  rest  // [2, 3, 4, 5]
  
  const [first, ...rest] = [];
  first // undefined
  rest  // []
  // 为什么这里是undefined而不是[]呢
  ```

- 字符串(有一个重要的好处，那就是能够正确识别四个字节的 Unicode 字符，正确返回字符串长度的函数)

  ```js
  const str = 'hi'
  [...str] // ['h','i']
  
  let str = 'x\uD83D\uDE80y';
  
  str.split('').reverse().join('')
  // 'y\uDE80\uD83Dx'
  
  [...str].reverse().join('')
  // 'y\uD83D\uDE80x'
  // 上面代码中，如果不用扩展运算符，字符串的reverse操作就不正确。
  ```

- 实现了Iterator接口的对象。任何定义了遍历器（Iterator）接口的对象，都可以用扩展运算符转为真正的数组。

- Map 和 Set 结构，Generator 函数

6. ### Array.from()

- 所谓类似数组的对象，本质特征只有一点，即必须有`length`属性。因此，任何有`length`属性的对象，都可以通过`Array.from`方法转为数组，而此时扩展运算符就无法转换。

    ```js
    Array.from({ length: 3 });
    // [ undefined, undefined, undefined ]
    // Array.from返回了一个具有三个成员的数组，每个位置的值都是undefined。扩展运算符转换不了这个对象。
    ```

- `Array.from`还可以接受第二个参数，作用类似于数组的`map`方法，用来对每个元素进行处理，将处理后的值放入返回的数组。

    ```js
    Array.from(arrayLike, x => x * x);
    // 等同于
    Array.from(arrayLike).map(x => x * x);
    ```

- Array.from()`可以将各种值转为真正的数组，并且还提供`map`功能。这实际上意味着，只要有一个原始的数据结构，你就可以先对它的值进行处理，然后转成规范的数组结构，进而就可以使用数量众多的数组方法。

    ```js
    Array.from({ length: 2 }, () => 'jack')
    // ['jack', 'jack']
    // Array.from的第一个参数指定了第二个参数运行的次数。这种特性可以让该方法的用法变得非常灵活。
    ```

- `Array.from()`的另一个应用是，将字符串转为数组，然后返回字符串的长度。因为它**能正确处理各种 Unicode 字符**，可以避免 JavaScript 将大于`\uFFFF`的 Unicode 字符，算作两个字符的 bug。

7. ### Array.of()

- `Array.of`方法用于将一组值，转换为数组。

  ```js
  Array.of(3, 11, 8) // [3,11,8]
  ```

- `Array.of`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。

- 模拟实现

  ```js
  function ArrayOf(){
    return [].slice.call(arguments);
  }
  ```

8. ### copyWithin() 

- 数组实例的`copyWithin`方法，在当前数组内部，将指定位置的成员复制到其他位置（会覆盖原有成员），然后返回当前数组。也就是说，使用这个方法，会修改当前数组。(看例子就好了)

  ```js
  // 将3号位复制到0号位
  [1, 2, 3, 4, 5].copyWithin(0, 3, 4)
  // [4, 2, 3, 4, 5]
  ```

9. ### find() & findIndex()

- 数组实例的`find`方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为`true`的成员，然后返回该成员。如果没有符合条件的成员，则返回`undefined`。`find`方法的回调函数可以接受三个参数，依次为当前的值、当前的位置和原数组。

- 数组实例的`findIndex`方法返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回`-1`。回调函数接受第二个参数，用来绑定回调函数的`this`对象。

  ```js
  function f(v){
    return v > this.age;
  }
  let person = {name: 'John', age: 20};
  [10, 12, 26, 15].find(f, person);    // 26
  ```

- 这两个方法都可以发现`NaN`，弥补了数组的`indexOf`方法的不足。

10. ### fill()

    `fill`方法使用给定值，填充一个数组。`fill`方法还可以接受第二个和第三个参数，用于指定填充的起始位置和结束位置。

    ```js
    let arr = new Array(3).fill([]);
    arr[0].push(5);
    arr
    // [[5], [5], [5]]
    // 为什么改变第一个 其他的都会改变 是同一个引用吗？？如何证明
    ```

11. ### entries() & keys() & values()

    用于遍历数组，它们都返回遍历器对象。`keys()`是对键名的遍历、`values()`是对键值的遍历，`entries()`是对键值对的遍历。

    ```js
    for (let index of ['a', 'b'].keys()) {
      console.log(index);
    }
    // 0
    // 1
    
    for (let elem of ['a', 'b'].values()) {
      console.log(elem);
    }
    // 'a'
    // 'b'
    
    for (let [index, elem] of ['a', 'b'].entries()) {
      console.log(index, elem);
    }
    // 0 "a"
    // 1 "b"
    // 这里要注意 [index, elem] 使用了结构赋值 
    for (let x of ['a','b'].entries()){
    	console.log(x)
    }
    [ 0, 'a' ]
    [ 1, 'b' ]
    ```

    ```js
    let letter = ['a', 'b', 'c'];
    let entries = letter.entries();
    console.log(entries.next().value); // [0, 'a']
    console.log(entries.next().value); // [1, 'b']
    console.log(entries.next().value); // [2, 'c']
    // 思考，这个指针可以指向之前的位置吗，往前？
    ```

12. ### includes()

    `Array.prototype.includes`方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的`includes`方法类似。该方法的第二个参数表示搜索的**起始**位置，默认为`0`。如果第二个参数为负数，则表示倒数的位置，如果这时它大于数组长度（比如第二个参数为`-4`，但数组长度为`3`），则会重置为从`0`开始。

    ```js
    [1, 2, 3].includes(2)     // true
    [1, 2, 3].includes(3, 3);  // false
    [1, 2, 3].includes(3, -1); // true
    ```

13. ### flat() & flatMap()

    `Array.prototype.flat()`用于将嵌套的数组“拉平”，`flat()`默认只会“拉平”一层，如果想要“拉平”多层的嵌套数组，可以将`flat()`方法的参数写成一个整数，表示想要拉平的层数，默认为1。如果原数组有空位，`flat()`方法会跳过空位。

    ```js
    [1, 2, [3, 4]].flat()
    // [1, 2, 3, 4]
    [1, 2, [3, [4, 5]]].flat(2)
    // [1, 2, 3, 4, 5]
    [1, [2, [3]]].flat(Infinity)
    // [1, 2, 3]
    [1, 2, , 4, 5].flat()
    // [1, 2, 4, 5]
    ```

    `flatMap()`方法对原数组的每个成员执行一个函数（相当于执行`Array.prototype.map()`），然后对返回值组成的数组执行`flat()`方法。该方法返回一个新数组，不改变原数组。

    ```js
    // 相当于 [[2, 4], [3, 6], [4, 8]].flat()
    [2, 3, 4].flatMap((x) => [x, x * 2])
    // [2, 4, 3, 6, 4, 8]
    ```

    `flatMap()`方法的参数是一个遍历函数，该函数可以接受三个参数，分别是当前数组成员、当前数组成员的位置（从零开始）、原数组。

    

14. 数组的空位：指数组的某一个位置没有任何值。空位不是`undefined`，一个位置的值等于`undefined`，依然是有值的。空位是没有任何值，`in`运算符可以说明这一点。

15. ES5对数组空位的处理

    - `forEach()`, `filter()`, `reduce()`, `every()` 和`some()`都会跳过空位。

    - `map()`会跳过空位，但会保留这个值

    - `join()`和`toString()`会将空位视为`undefined`，而`undefined`和`null`会被处理成空字符串。

16. ES6对数组空位的处理

    - ES6 则是明确将空位转为`undefined`。

    - `Array.from`方法会将数组的空位，转为`undefined`，也就是说，这个方法不会忽略空位。
    - 扩展运算符（`...`）也会将空位转为`undefined`。
    - `copyWithin()`会连空位一起拷贝。
    - `fill()`会将空位视为正常的数组位置。
    - `for...of`循环也会遍历空位。
    - `entries()`、`keys()`、`values()`、`find()`和`findIndex()`会将空位处理成`undefined`。



## 疑惑

1. 这个是什么功能，`…items` 参数的意思？是把items从数组变为序列吗？

   ```js
   function push(array, ...items) {
     array.push(...items);
   }
   ```

2. 这两种写法等价吗

   ```javascript
   [].slice.call(obj)
   Array.prototype.slice.call(obj)
   ```

3. 数组的负数索引？

   -1相当于数组最后一位

4. `Array.from()` `Array.of()` 两者区别？

5. Array.of`基本上可以用来替代`Array()`或`new Array()`，并且不存在由于参数不同而导致的重载。它的行为非常统一。这里说的重载是什么意思？

6. `indexOf` 与 `includes` 的区别？

    `indexOf`方法有两个缺点，一是不够语义化，它的含义是找到参数值的第一个出现位置，所以要去比较是否不等于`-1`，表达起来不够直观。二是，它内部使用严格相等运算符（`===`）进行判断，这会导致对`NaN`的误判。

7. 这段代码的参数怎么理解

   ```javascript
   const contains = (() =>
     Array.prototype.includes
       ? (arr, value) => arr.includes(value)
       : (arr, value) => arr.some(el => el === value)
   )();
   contains(['foo', 'bar'], 'baz'); // => false
   ```

8. 为什么这里是两个空位？

   ```js
   let arr = [, ,];
   for (let i of arr) {
     console.log(1);
   }
   // 1
   // 1
   // 数组arr有两个空位，for...of并没有忽略它们。如果改成map方法遍历，空位是会跳过的
   ```

   