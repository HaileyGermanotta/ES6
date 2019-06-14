# 4-字符串的扩展

学习时间:  120min



## 知识点汇总

1. ES6 加强了对 Unicode 的支持，并且扩展了字符串对象。

2. JavaScript 允许采用`\uxxxx`形式表示一个字符，其中`xxxx`表示字符的 Unicode 码点。这种表示法只限于码点在`\u0000`~`\uFFFF`之间的字符。超出这个范围的字符，必须用两个双字节的形式表示。只要将码点放入大括号，就能正确解读该字符。

   ```javascript
   "\u20BB7"
   // " 7"

   "\u{20BB7}"
   // "𠮷"
   ```

3. `codePointAt()`:`charAt`方法无法读取整个字符，`charCodeAt`方法只能分别返回前两个字节和后两个字节的值。后者会正确返回 32 位的 UTF-16 字符的码点，返回的是码点的十进制值，如果想要十六进制的值，可以使用`toString`方法转换。

4. `for...of`循环可以正确识别 32 位的 UTF-16 字符。

   ```javascript
   let s = '𠮷a';
   for (let ch of s) {
     console.log(ch.codePointAt(0).toString(16));
   }
   // 20bb7
   // 61
   ```

5. `codePointAt`方法是测试一个字符由两个字节还是由四个字节组成的最简单方法。原理是：需要`4`个字节储存的字符（Unicode 码点大于`0xFFFF`的字符）。

   ```js
   function is32Bit(c) {
     return c.codePointAt(0) > 0xFFFF;
   }

   is32Bit("𠮷") // true
   is32Bit("a") // false
   ```

6. `String.fromCharCode`方法，用于从码点返回对应字符，但是这个方法不能识别 32 位的 UTF-16 字符。(ES5)

   `String.fromCodePoint`方法，可以识别大于`0xFFFF`的字符。(ES6) 如果有多个参数，则它们会被合并成一个字符串返回。

7. ES6 为字符串添加了遍历器接口，使得字符串可以被`for...of`循环遍历。遍历器最大的优点是可以识别大于`0xFFFF`的码点。传统的`for`循环无法识别这样的码点。

8. ES5 对字符串对象提供`charAt`方法，返回字符串给定位置的字符，该方法不能识别码点大于`0xFFFF`的字符。

   ES6对字符串实例提供`at`方法，可以识别 Unicode 编号大于`0xFFFF`的字符，返回正确的字符。

9. 许多欧洲语言有语调符号和重音符号。为了表示它们，Unicode 提供了两种方法。一种是直接提供带重音符号的字符，比如`Ǒ`（\u01D1）。另一种是提供合成符号（combining character），即原字符与重音符号的合成，两个字符合成一个字符，比如`O`（\u004F）和`ˇ`（\u030C）合成`Ǒ`（\u004F\u030C）。ES6 提供字符串实例的`normalize()`方法，用来将字符的不同表示方法统一为同样的形式，这称为 Unicode 正规化。

10. `normalize`方法可以接受一个参数来指定`normalize`的方式，有四个参数可选值。不过，`normalize`方法目前不能识别三个或三个以上字符的合成。这种情况下，还是只能使用正则表达式，通过 Unicode 编号区间判断。

11. ES5: `indexOf`

    ES6: 这三个方法都支持第二个参数，表示开始搜索的位置。

    `includes()`：返回布尔值，表示是否找到了参数字符串。

    `startsWith()`：返回布尔值，表示参数字符串是否在原字符串的头部。

    `endsWith()`：返回布尔值，表示参数字符串是否在原字符串的尾部。

12. `repeat`方法返回一个新字符串，表示将原字符串重复`n`次。

13. 如果某个字符串不够指定长度，会在头部或尾部补全。`padStart()`用于头部补全，`padEnd()`用于尾部补全。如果省略第二个参数，默认使用**空格**补全长度。`padStart`的常见用途是为数值补全指定位数,另一个用途是提示字符串格式。

14. `matchAll`方法返回一个正则表达式在当前字符串的所有匹配。

15. **模板字符串**（template string）是增强版的字符串，用反引号（\`）标识。它可以当作普通字符串使用，也可以用来定义多行字符串，或者在字符串中嵌入变量。

    Attention:

    (1)如果使用模板字符串表示多行字符串，所有的空格和缩进都会被保留在输出之中，如果不想要这个换行，可以使用`trim`方法消除它。

    (2)如果在模板字符串中需要使用反引号，则前面要用反斜杠转义。

    (3)模板字符串中嵌入变量，需要将变量名写在`${}`之中，大括号内部可以放入任意的 JavaScript 表达式，可以进行运算，以及引用对象属性。

     (4)模板字符串之中还能调用函数。

    (5)如果大括号中的值不是字符串，将按照一般的规则转为字符串。比如，大括号中是一个对象，将默认调用对象的`toString`方法。如果大括号内部是一个字符串，将会原样输出。

    (6)模板字符串甚至还能嵌套。

    ```js
    $('#result').append(`
      There are <b>${basket.count}</b> items
       in your basket, <em>${basket.onSale}</em>
      are on sale!
    `);
    ```

16. **标签模板**（tagged template）标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。“标签模板”的应用，(1)就是过滤 HTML 字符串，防止用户输入恶意内容。(2)多语言转换（国际化处理）。(3)在 JavaScript 语言之中嵌入其他语言。

17. 模板处理函数的第一个参数（模板字符串数组），还有一个`raw`属性。

18. ES6 还为原生的 String 对象，提供了一个`raw`方法。`String.raw`方法，往往用来充当模板字符串的处理函数，返回一个斜杠都被转义（即斜杠前面再加一个斜杠）的字符串，对应于替换变量后的模板字符串。如果原字符串的斜杠已经转义，那么`String.raw`会进行再次转义。

19. 模板字符串的限制：ES2018 [放松](https://tc39.github.io/proposal-template-literal-revision/)了对标签模板里面的字符串转义的限制。如果遇到不合法的字符串转义，就返回`undefined`，而不是报错，并且从`raw`属性上面可以得到原始字符串。

    



## 疑惑

Q: What is Unicode?

A: Unicode源于一个很简单的想法：将全世界所有的字符包含在一个集合里，计算机只要支持这一个字符集，就能显示所有的字符，再也不会有乱码了。



Q: 

> JavaScript 共有 6 种方法可以表示一个字符。

```
'\z' === 'z'  // true
'\172' === 'z' // true
'\x7A' === 'z' // true
'\u007A' === 'z' // true
'\u{7A}' === 'z' // true
```

除了这一章介绍的两种：大括号表示法和UTF-16编码还有哪四种？



Q:

> 注意，`fromCodePoint`方法定义在`String`对象上，而`codePointAt`方法定义在字符串的实例对象上。

（如何理解）



Q:

>如果需要引用模板字符串本身，在需要时执行，可以像下面这样写。
>
>```js
>// 写法一
>let str = 'return ' + '`Hello ${name}!`';
>let func = new Function('name', str);
>func('Jack') // "Hello Jack!"
>
>// 写法二
>let str = '(name) => `Hello ${name}!`';
>let func = eval.call(null, str);
>func('Jack') // "Hello Jack!"
>```

(不理解) Function两个参数如何理解



Q:模板编译这块没看懂



## 参考链接

[Unicode与JavaScript详解](http://www.ruanyifeng.com/blog/2014/12/unicode.html)