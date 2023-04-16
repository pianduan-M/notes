## Js 中的隐私转换

在阅读 《你不知道的 JavaScript》一书中看到以下代码，对其中的一些类型的隐式转换比较有意思。

```
        "0" == null;           // false
        "0" == undefined;      // false
        "0" == false;          // true -- 晕！
        "0" == NaN;            // false
        "0" == 0;              // true
        "0" == "";             // false

        false == null;         // false
        false == undefined;    // false
        false == NaN;          // false
        false == 0;            // true -- 晕！
        false == "";           // true -- 晕！
        false == [];           // true -- 晕！
        false == {};           // false

        "" == null;            // false
        "" == undefined;       // false
        "" == NaN;             // false
        "" == 0;               // true -- 晕！
        "" == [];              // true -- 晕！
        "" == {};              // false

        0 == null;             // false
        0 == undefined;        // false
        0 == NaN;              // false
        0 == [];               // true -- 晕！
        0 == {};               // false
```

1.  "0" == null

null 只跟 null,undefined 比较时为 true, 其他皆为 false

2. "0" == undefined

   undefined 跟 null 一样，只跟自身或 null 比较时为 true

3. "0" == false;

其他类型跟布尔类型比较时

规范 11.9.3.6-7 是这样说的：

(1) 如果 Type(x)是布尔类型，则返回 ToNumber(x) == y 的结果；

(2) 如果 Type(y)是布尔类型，则返回 x == ToNumber(y)的结果

所以 "0" == false 先被转换成 "0" == 0 ，然后 "0" 根据规范转换成 0 ，所以结果为 true

4.  "0" == NaN;

NaN 跟任何值比较都返回 false , 包括自身。

5.  "0" == 0;

根据规范字符串跟数字类型比较，字符串先被转换成 number 类型

6. "0" == "";

   相同类型直接比较，结果为 false

7. false == null;

   null 只跟 null,undefined 比较时为 true, 其他皆为 false

8. false == undefined;

   undefined 跟 null 一样，只跟自身或 null 比较时为 true

9. false == NaN;

   NaN 跟任何值比较都返回 false , 包括自身。

10. false == 0;

    根据规范 false 先被转换成数字类型再进行比较 0 == 0 结果为 true

11. false == ""

    根据规范布尔值先转换成数字类型 0 == "" ，然后字符串类型跟数字类型比较字符串转换成数字类型 "" 转换成数字为 0 所以最后是 0 == 0 结果为 true

12. false == [];

    根据规范 false == [] 转换后为 0 == []，对象类型跟数字或字符串类型比较时会进行 ToPrimitive 转换（先调用对象的 valueOf 方法返回值如果不是基本数据类型，再调用 toString 方法）空数组 toString 返回 "" ，此时变成 0 == "" 结果为 true

13. false == {};

    根据规范 false == {} 转换后为 0 == {}，对象类型跟数字或字符串类型比较时会进行 ToPrimitive 转换（先调用对象的 valueOf 方法返回值如果不是基本数据类型，再调用 toString 方法）对象的 valueOf 方法对象本身，接着调用 toString 返回 "[object Object]" ，此时变成 0 == "[object Object]" ，"[object Object]" 转换成数字为 NaN ，NaN 跟任何值比较为 false，结果为 false

14. "" == null;


    null 只跟 null,undefined 比较时为 true, 其他皆为 false

15. "" == undefined;

    undefined 跟 null 一样，只跟自身或 null 比较时为 true

16. "" == NaN;

    NaN 跟任何值比较都返回 false , 包括自身。

17. "" == 0

    根据规范字符串跟数字类型比较，字符串先转换成数字类型，'' 转换成数字类型为 0，所以结果为 0 == 0 true

18. "" == [];

    [] 进行 ToPrimitive 转换变成 '' == '' ，结果为 true

19. "" == {};

    {} 进行 ToPrimitive 转换成 '' == '[object Object]' 结果为 false

20. 0 == null;

    null 只跟 null,undefined 比较时为 true, 其他皆为 false

21. 0 == undefined;

    undefined 跟 null 一样，只跟自身或 null 比较时为 true

22. 0 == NaN;

    NaN 跟任何值比较都返回 false , 包括自身。

23. 0 == []

    [] 进行 ToPrimitive 转换变成 0 == '' ，'' 转换成数字类型为 0，0 == 0 结果为 true

24. 0 == {};

    {} 进行 ToPrimitive 转换成 0 == '[object Object]' ，'[object Object]' 转换成数字为 NaN，0 == NaN ，结果为 false

|            | 被比较值 B |           |                       |                               |                                 |                                 |              |
| :--------- | :--------- | --------- | --------------------- | ----------------------------- | ------------------------------- | ------------------------------- | ------------ |
|            |            | Undefined | Null                  | Number                        | String                          | Boolean                         | Object       |
| 被比较值 A | Undefined  | `true`    | `true`                | `false`                       | `false`                         | `false`                         | `IsFalsy(B)` |
| Null       | `true`     | `true`    | `false`               | `false`                       | `false`                         | `IsFalsy(B)`                    |              |
| Number     | `false`    | `false`   | `A === B`             | `A === ToNumber(B)`           | `A=== ToNumber(B)`              | `A== ToPrimitive(B)`            |              |
| String     | `false`    | `false`   | `ToNumber(A) === B`   | `A === B`                     | `ToNumber(A) === ToNumber(B)`   | `ToPrimitive(B) == A`           |              |
| Boolean    | `false`    | `false`   | `ToNumber(A) === B`   | `ToNumber(A) === ToNumber(B)` | `A === B`                       | `ToNumber(A) == ToPrimitive(B)` |              |
| Object     | `false`    | `false`   | `ToPrimitive(A) == B` | `ToPrimitive(A) == B`         | `ToPrimitive(A) == ToNumber(B)` | `A === B`                       |              |
