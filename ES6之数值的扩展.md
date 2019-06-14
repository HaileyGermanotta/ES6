# 7-数值的扩展

学习时间:  60min



## 知识点汇总

1. ES6 提供了二进制和八进制数值的新的写法，分别用前缀`0b`（或`0B`）和`0o`（或`0O`）表示。

2. 如果要将`0b`和`0o`前缀的字符串数值转为十进制，要使用`Number`方法。

   ```js
   Number('0b111')  // 7
   Number('0o10')  // 8
   ```

3. `Number.isFinite()`用来检查一个数值是否为有限的（finite），即不是`Infinity`。如果参数类型不是数值，`Number.isFinite`一律返回`false`。(与传统不同在于 只对数值有效)

4. `Number.isNaN()`用来检查一个值是否为`NaN`。如果参数类型不是`NaN`，`Number.isNaN`一律返回`false`。

5. ES6 将全局方法`parseInt()`和`parseFloat()`，移植到`Number`对象上面，行为完全保持不变。

6. `Number.isInteger()`用来判断一个数值是否为整数。如果参数不是数值，`Number.isInteger`返回`false`。

   >由于 JavaScript 采用 IEEE 754 标准，数值存储为64位双精度格式，数值精度最多可以达到 53 个二进制位（1 个隐藏位与 52 个有效位）。如果数值的精度超过这个限度，第54位及后面的位就会被丢弃，这种情况下，`Number.isInteger`可能会误判。

   >如果一个数值的绝对值小于`Number.MIN_VALUE`（5E-324），即小于 JavaScript 能够分辨的最小值，会被自动转为 0。这时，`Number.isInteger`也会误判。

7. ES6 在`Number`对象上面，新增一个极小的常量`Number.EPSILON`。根据规格，它表示 1 与大于 1 的最小浮点数之间的差。引入一个这么小的量的目的，在于为浮点数计算，设置一个误差范围。

8. `Number.EPSILON`可以用来设置“能够接受的误差范围”。比如，误差范围设为 2 的-50 次方（即`Number.EPSILON * Math.pow(2, 2)`），即如果两个浮点数的差小于这个值，我们就认为这两个浮点数相等。因此，`Number.EPSILON`的实质是一个可以接受的最小误差范围。

9. JavaScript 能够准确表示的整数范围在`-2^53`到`2^53`之间（不含两个端点），超过这个范围，无法精确表示这个值。ES6 引入了`Number.MAX_SAFE_INTEGER`和`Number.MIN_SAFE_INTEGER`这两个常量，用来表示这个范围的上下限。

   ```js
   Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
   // true
   Number.MAX_SAFE_INTEGER === 9007199254740991
   // true

   Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
   // true
   Number.MIN_SAFE_INTEGER === -9007199254740991
   // true
   ```

10. `Number.isSafeInteger()`则是用来判断一个整数是否落在这个范围（`-2^53`到`2^53`之间）之内。

11. 验证运算结果是否落在安全整数的范围内，不要只验证运算结果，而要同时验证参与运算的每个值。如果只验证运算结果是否为安全整数，很可能得到错误结果。

12. ES6 在 Math 对象上新增了 17 个与数学相关的方法。所有这些方法都是静态方法，只能在 Math 对象上调用。

    - `Math.trunc`方法用于去除一个数的小数部分，返回整数部分。对于非数值，`Math.trunc`内部使用`Number`方法将其先转为数值。对于空值和无法截取整数的值，返回`NaN`。

    - `Math.sign`方法用来判断一个数到底是正数、负数、还是零。对于非数值，会先将其转换为数值。会返回`-1` `+1` `0` `-0` `NaN`

    - `Math.cbrt`方法用于计算一个数的立方根。对于非数值，会先将其转换为数值。

    - JavaScript 的整数使用 32 位二进制形式表示，`Math.clz32`方法返回一个数的 32 位无符号整数形式有多少个前导 0。(count leading zero bits in 32-bit binary representation of a number)。左移运算符（`<<`）与`Math.clz32`方法直接相关。对于小数，`Math.clz32`方法只考虑整数部分。对于空值或其他类型的值，`Math.clz32`方法会将它们先转为数值，然后再计算。

    - `Math.imul`方法返回两个数以 32 位带符号整数形式相乘的结果，返回的也是一个 32 位的带符号整数。`Math.imul(a, b)`与`a * b`的结果是相同的，即该方法等同于`(a * b)|0`的效果（超过 32 位的部分溢出）。`Math.imul`方法可以返回正确的低位数值。

    - `Math.fround`方法返回一个数的32位单精度浮点数形式。`Math.fround`方法的主要作用，是将64位双精度浮点数转为32位单精度浮点数。如果小数的精度超过24个二进制位，返回值就会不同于原值，否则返回值不变（即与64位双精度值一致）。对于 `NaN` 和 `Infinity`，此方法返回原值。对于其它类型的非数值，`Math.fround` 方法会先将其转为数值，再返回单精度浮点数。

      ```js
      // 未丢失有效精度
      Math.fround(1.125) // 1.125
      Math.fround(7.25)  // 7.25

      // 丢失精度
      Math.fround(0.3)   // 0.30000001192092896
      Math.fround(0.7)   // 0.699999988079071
      Math.fround(1.0000000123) // 1
      ```

13. `Math.hypot`方法返回所有参数的平方和的平方根。如果参数不是数值，`Math.hypot`方法会将其转为数值。只要有一个参数无法转为数值，就会返回 NaN。

14. ES6 新增了 4 个对数相关方法。

    - `Math.expm1(x)`返回 ex - 1，即`Math.exp(x) - 1`。
    - `Math.log1p(x)`方法返回`1 + x`的自然对数，即`Math.log(1 + x)`。如果`x`小于-1，返回`NaN`。
    - `Math.log10(x)`返回以 10 为底的`x`的对数。如果`x`小于 0，则返回 NaN。
    - `Math.log2(x)`返回以 2 为底的`x`的对数。如果`x`小于 0，则返回 NaN。

15. ES6 新增了 6 个双曲函数方法。

    - `Math.sinh(x)` 返回`x`的双曲正弦（hyperbolic sine）
    - `Math.cosh(x)` 返回`x`的双曲余弦（hyperbolic cosine）
    - `Math.tanh(x)` 返回`x`的双曲正切（hyperbolic tangent）
    - `Math.asinh(x)` 返回`x`的反双曲正弦（inverse hyperbolic sine）
    - `Math.acosh(x)` 返回`x`的反双曲余弦（inverse hyperbolic cosine）
    - `Math.atanh(x)` 返回`x`的反双曲正切（inverse hyperbolic tangent）

16. ES2016 新增了一个指数运算符（`**`）。指数运算符可以与等号结合，形成一个新的赋值运算符（`**=`）。

    >在 V8 引擎中，指数运算符与`Math.pow`的实现不相同，对于特别大的运算结果，两者会有细微的差异。

    




