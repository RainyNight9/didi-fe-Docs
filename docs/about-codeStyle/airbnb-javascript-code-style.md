# Airbnb JavaScript 编码风格指南(2018年最新版)
[返回首页](../../README.md)

## 类型
1. 基本类型：直接存取
    - `string`
    - `number`
    - `boolean`
    - `null`
    - `undefined`
    - `symbol`

    ```javascript
    const foo = 1;
    let bar = foo;
    
    bar = 9;
    
    console.log(foo, bar); // => 1, 9
    ```
    - `symbol` 类型不能完全polyfilled，所以请谨慎使用
2. 复杂类型: 通过引用的方式存取
    - `object`
    - `array`
    - `function`
    
    ```javascript
    const foo = [1, 2];
    const bar = foo;
    
    bar[0] = 9;
    
    console.log(foo[0], bar[0]); // => 9, 9
    ```

## 引用
1. 使用`const`申明引用类型，避免使用`var`。eslint 设置：[prefer-const](https://eslint.org/docs/rules/prefer-const.html),[no-const-assign](https://eslint.org/docs/rules/no-const-assign.html)
    > 为什么？这能确保你无法对引用重新赋值，也不会导致出现 bug 或难以理解。

    ```javascript
    // bad
    var a = 1;
    var b = 2;
    
    // good
    const a = 1;
    const b = 2;
    ```
2. 如果必须对引用类型重新赋值，使用`let`而非`var`。eslint设置：[no-var](https://eslint.org/docs/rules/no-var.html) jscs: [disallowVar](http://jscs.info/rule/disallowVar)
    >  为什么？相比于`var`函数作用域，`let`块级作用域更容易理解  
    
    ```javascript
    // bad
    var count = 1;
    if (true) {
      count += 1;
    }
    
    // good, use the let.
    let count = 1;
    if (true) {
      count += 1;
    }
    ```
3. 注意`let`和`const`都是块级作用域

    ```javascript
    // const and let only exist in the blocks they are defined in.
    {
      let a = 1;
      const b = 1;
    }
    console.log(a); // ReferenceError
    console.log(b); // ReferenceError
    ```

## 对象
1. 使用字面值创建对象。eslint: [no-new-object](https://eslint.org/docs/rules/no-new-object.html)

    ```javascript
    // bad
    const item = new Object();
    
    // good
    const item = {};
    ```
2. 创建对象的动态属性时，使用计算属性
    > 为什么？这样可以在一个地方定义对象所有的属性

    ```javascript
    function getKey(k) {
      return `a key named ${k}`;
    }
    
    // bad
    const obj = {
      id: 5,
      name: 'San Francisco',
    };
    obj[getKey('enabled')] = true;
    
    // good
    const obj = {
      id: 5,
      name: 'San Francisco',
      [getKey('enabled')]: true,
    };
    ```
3. 使用对象方法的简写形式。 eslint: [object-shorthand](https://eslint.org/docs/rules/object-shorthand.html) jscs: [requireEnhancedObjectLiterals](http://jscs.info/rule/requireEnhancedObjectLiterals)
    > 为什么？方法定义简洁清晰

    ```javascript
    // bad
    const atom = {
      value: 1,
    
      addValue: function (value) {
        return atom.value + value;
      },
    };
    
    // good
    const atom = {
      value: 1,
    
      addValue(value) {
        return atom.value + value;
      },
    };
    ```
4. 使用属性值简写形式。eslint: [object-shorthand](https://eslint.org/docs/rules/object-shorthand.html) jscs: [requireEnhancedObjectLiterals]
    > 为什么？书写更加简洁，更有描述性。

    ```javascript
    const lukeSkywalker = 'Luke Skywalker';
    
    // bad
    const obj = {
      lukeSkywalker: lukeSkywalker,
    };
    
    // good
    const obj = {
      lukeSkywalker,
    };
    ```
5. 对象声明时分类简写和非简写的属性名。
    > 为什么？更清晰的了解哪些属性是简写的。

    ```javascript
    const anakinSkywalker = 'Anakin Skywalker';
    const lukeSkywalker = 'Luke Skywalker';
    
    // bad
    const obj = {
      episodeOne: 1,
      twoJediWalkIntoACantina: 2,
      lukeSkywalker,
      episodeThree: 3,
      mayTheFourth: 4,
      anakinSkywalker,
    };
    
    // good
    const obj = {
      lukeSkywalker,
      anakinSkywalker,
      episodeOne: 1,
      twoJediWalkIntoACantina: 2,
      episodeThree: 3,
      mayTheFourth: 4,
    };
    ```
6. 只有对那些不合法的属性名标识符添加引号。eslint: [quote-props](https://eslint.org/docs/rules/quote-props.html) jscs: [disallowQuotedKeysInObjects](http://jscs.info/rule/disallowQuotedKeysInObjects)
    > 为什么？对象属性更直观，可读性强。能够代码高亮显示，同时对于大多数的js引擎更容易优化代码。

    ```javascript
    // bad
    const bad = {
      'foo': 3,
      'bar': 4,
      'data-blah': 5,
    };
    
    // good
    const good = {
      foo: 3,
      bar: 4,
      'data-blah': 5,
    };
    ```
7. 不要直接使用`Object.prototype`上的方法，例如`hasOwnProperty`, `propertyIsEnumerable`, 和 `isPrototypeOf`。
    > 为什么？这些方法可能受对象的其他属性影响。例如`{ hasOwnProperty: false }` 或者 对象可能是null(`Object.create(null)`)

    ```javascript
    // bad
    console.log(object.hasOwnProperty(key));
    
    const object = Object.create(null);
    obj.hasOwnProperty(key) // Uncaught TypeError: obj.hasOwnProperty is not a function
    
    // good
    console.log(Object.prototype.hasOwnProperty.call(object, key));
    
    // best
    const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
    /* or */
    import has from 'has'; // https://www.npmjs.com/package/has
    // ...
    console.log(has.call(object, key));
    
    ```
8. 浅拷贝对象时推荐使用对象展开操作（object spread operator）而不是`Object.assign`。使用对象剩余操作符（object rest operator）获取对象中剩余的属性。
    > 为什么？`Object.assign`使用不当会修改原对象

    ```javascript
    // very bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign(original, { c: 3 }); // this mutates `original` ಠ_ಠ
    delete copy.a; // so does this
    
    // bad
    const original = { a: 1, b: 2 };
    const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }
    
    // good
    const original = { a: 1, b: 2 };
    const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }
    
    const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
    ```

## 数组
1. 使用字面量声明数组。eslint: [no-array-constructor](https://eslint.org/docs/rules/no-array-constructor.html)

    ```javascript
    // bad
    const items = new Array();
    
    // good
    const items = [];
    ```

2. 向数组添加元素时，使用`Arrary#push`替代直接赋值。

    ```javascript
    const someStack = [];
    
    // bad
    someStack[someStack.length] = 'abracadabra';
    
    // good
    someStack.push('abracadabra');
    ```
3. 使用数组展开操作符`...`拷贝数组

    ```javascript
    // bad
    const len = items.length;
    const itemsCopy = [];
    let i;
    
    for (i = 0; i < len; i += 1) {
      itemsCopy[i] = items[i];
    }
    
    // good
    const itemsCopy = [...items];
    ```
4. 将类数组对象（array-like）转换成数组时，使用`...`而不是`Array.from`
    
    ```javascript
    const foo = document.querySelectorAll('.foo');

    // good
    const nodes = Array.from(foo);
    
    // best
    const nodes = [...foo];
    ```
5. 当需要对可遍历对象进行map操作时，使用`Array.from`而不是展开操作符`...`,避免新建一个临时数组。

    ```javascript
    // bad
    const baz = [...foo].map(bar);
    
    // good
    const baz = Array.from(foo, bar);
    ```

6. 数组方法回调需要有返回值。如果函数体比较简单，可以直接用表达式，省略`return`语句。 eslint: [array-callback-return](https://eslint.org/docs/rules/array-callback-return)

    ```javascript
    // good
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
    
    // good
    [1, 2, 3].map(x => x + 1);
    
    // bad - no returned value means `memo` becomes undefined after the first iteration
    [[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
      const flatten = memo.concat(item);
      memo[index] = flatten;
    });
    
    // good
    [[0, 1], [2, 3], [4, 5]].reduce((memo, item, index) => {
      const flatten = memo.concat(item);
      memo[index] = flatten;
      return flatten;
    });
    
    // bad
    inbox.filter((msg) => {
      const { subject, author } = msg;
      if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
      } else {
        return false;
      }
    });
    
    // good
    inbox.filter((msg) => {
      const { subject, author } = msg;
      if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
      }
    
      return false;
    });
    ```
7. 如果数组有多行，请在打开和关闭数组括号之前使用换行符
    > 为什么？ 更具有可读性

    ```javascript
    // bad
    const arr = [
      [0, 1], [2, 3], [4, 5],
    ];
    
    const objectInArray = [{
      id: 1,
    }, {
      id: 2,
    }];
    
    const numberInArray = [
      1, 2,
    ];
    
    // good
    const arr = [[0, 1], [2, 3], [4, 5]];
    
    const objectInArray = [
      {
        id: 1,
      },
      {
        id: 2,
      },
    ];
    
    const numberInArray = [
      1,
      2,
    ];
    ```

## 解构
1. 访问和使用对象的多个属性时用对象解构操作。eslint: [prefer-destructuring](https://eslint.org/docs/rules/prefer-destructuring) jscs: [requireObjectDestructuring](http://jscs.info/rule/requireObjectDestructuring) 
    > 为什么？解构可以避免为这些属性创建临时引用。

    ```javascript
    // bad
    function getFullName(user) {
      const firstName = user.firstName;
      const lastName = user.lastName;
    
      return `${firstName} ${lastName}`;
    }
    
    // good
    function getFullName(user) {
      const { firstName, lastName } = user;
      return `${firstName} ${lastName}`;
    }
    
    // best
    function getFullName({ firstName, lastName }) {
      return `${firstName} ${lastName}`;
    }
    ```
2. 使用数组解构。eslint: [prefer-destructuring](https://eslint.org/docs/rules/prefer-destructuring) jscs: [requireArrayDestructuring](http://jscs.info/rule/requireArrayDestructuring)

    ```javascript
    const arr = [1, 2, 3, 4];
    
    // bad
    const first = arr[0];
    const second = arr[1];
    
    // good
    const [first, second] = arr;
    ```
3. 使用对象解构来实现多个返回值，而不是数组解构。jscs: [disallowArrayDestructuringReturn](http://jscs.info/rule/disallowArrayDestructuringReturn)
    > 为什么？你可以随时为返回值新增属性而不用关心属性的顺序。

    ```javascript
    // bad
    function processInput(input) {
      // then a miracle occurs
      return [left, right, top, bottom];
    }
    
    // 调用者需要注意返回值中对象的顺序
    const [left, __, top] = processInput(input);
    
    // good
    function processInput(input) {
      // then a miracle occurs
      return { left, right, top, bottom };
    }
    
    // 调用者只需要使用它需要的对象
    const { left, top } = processInput(input);
    ```

## 字符串
1. 字符串使用单引号。eslint: [quotes](https://eslint.org/docs/rules/quotes.html) jscs: [validateQuoteMarks](http://jscs.info/rule/validateQuoteMarks)

    ```javascript
    // bad
    const name = "Capt. Janeway";
    
    // bad - 当需要插值或者换行时才使用模板文字
    const name = `Capt. Janeway`;
    
    // good
    const name = 'Capt. Janeway';
    ```
2. 不超过100个字符的字符串不应该使用连接符或者换行书写。
    > 为什么？换行的字符串不好阅读，并且不方便搜索代码。

    ```javascript
    // bad
    const errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';
    
    // bad
    const errorMessage = 'This is a super long error that was thrown because ' +
      'of Batman. When you stop to think about how Batman had anything to do ' +
      'with this, you would get nowhere fast.';
    
    // good
    const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
    ```
3. 以编程方式构建字符串时，使用模板字符串而不是连接符。eslint: [prefer-template](https://eslint.org/docs/rules/prefer-template.html) [template-curly-spacing](https://eslint.org/docs/rules/template-curly-spacing) jscs: [requireTemplateStrings](http://jscs.info/rule/requireTemplateStrings)
    > 为什么？模板字符串更为简洁，更具可读性。
    
    ```javascript
    // bad
    function sayHi(name) {
      return 'How are you, ' + name + '?';
    }
    
    // bad
    function sayHi(name) {
      return ['How are you, ', name, '?'].join();
    }
    
    // bad
    function sayHi(name) {
      return `How are you, ${ name }?`;
    }
    
    // good
    function sayHi(name) {
      return `How are you, ${name}?`;
    }
    ```
4. 永远不要在字符串上使用`eval()`方法，它有太多的问题。eslint: [no-eval](https://eslint.org/docs/rules/no-eval)

5. 不要过多的转义字符串。eslint: [no-useless-escape](https://eslint.org/docs/rules/no-useless-escape) 
    > 为什么？反斜杠影响代码可读性，只有在必要的时候才使用。

    ```javascript
    // bad
    const foo = '\'this\' \i\s \"quoted\"';
    
    // good
    const foo = '\'this\' is "quoted"';
    const foo = `my name is '${name}'`;
    ```

## 函数
1. 使用命名函数表达式而不是函数声明。eslint: [func-style](https://eslint.org/docs/rules/func-style) jscs: [disallowFunctionDeclarations](http://jscs.info/rule/disallowFunctionDeclarations)
    > 为什么？函数声明会被提前。这意味着很可能在函数定义前引用该函数，但是不会报错。这不利于代码的可读性和可维护性。如果你发现一个函数定义的很大很复杂，以至于妨碍了了解文件中的其他内容，那么是时候把这个函数提取到自己的模块中去了！不要忘记显示指定表达式的名称，尽管它能从变量名中被推断出来（现代浏览器或者编译器（如Babel）支持）。这能让错误的调用栈更清晰。([讨论](https://github.com/airbnb/javascript/issues/794))

    ```javascript
    // bad
    function foo() {
      // ...
    }
    
    // bad
    const foo = function () {
      // ...
    };
    
    // good
    // 函数名和变量引用名不同
    const short = function longUniqueMoreDescriptiveLexicalFoo() {
      // ...
    };
    ```
    
    ```javascript
    // Is it worse
    const sum = function(a, b) {
      return a + b;
    };
    
    // than this?
    const my_sum = function sum(a, b) {
      return a + b;
    };
    ```
    > 第一个函数没有`.name`属性，在debugging过程中，它会是一个匿名函数。第二个函数有名字为`sum`，你可以检索到它，调试过程中能够快速定位。
    
    > 使用banel 和`babel-preset-env`配置，`const foo = () => {}`会转换成`var foo = function foo () {}`，并且从Node v6开始，`const foo = () => {}`中的foo 也有`.name`。所以它不再是匿名函数。（函数名字推断）
    
2. 用圆括号包裹立即执行函数表达式（IIFE）。eslint: [wrap-iife](https://eslint.org/docs/rules/wrap-iife.html) jscs: [requireParenthesesAroundIIFE](http://jscs.info/rule/requireParenthesesAroundIIFE) 
    > 为什么？ 立即执行函数表达式是单一执行单元-使用圆括号包裹调用，简洁明了的表示了这一点。请注意，在通用的模块中，你几乎用不到IIFE。

    ```javascript
    // immediately-invoked function expression (IIFE)
    (function () {
      console.log('Welcome to the Internet. Please follow me.');
    }());
    ```
3. 永远不要在一个非函数代码块（if、while 等）中声明一个函数，把那个函数赋给一个变量。浏览器允许你这么做，但它们的解析表现不一致。eslint: [no-loop-func](https://eslint.org/docs/rules/no-loop-func.html)
4. 注意：ECMA-262把`block`定义为一组语句。但是函数声明不是语句。

    ```javascript
    // bad
    if (currentUser) {
      function test() {
        console.log('Nope.');
      }
    }
    
    // good
    let test;
    if (currentUser) {
      test = () => {
        console.log('Yup.');
      };
    }
    ```
5. 永远不要把参数命名为`arguments`。这将取代原来函数作用域内的 `arguments`对象。

    ```javascript
    // bad
    function foo(name, options, arguments) {
      // ...
    }
    
    // good
    function foo(name, options, args) {
      // ...
    }
    ```
6. 不要使用`arguments`。可以选择 rest 语法 `...` 替代。
    >  为什么？使用 `...` 能明确你要传入的参数。另外 rest 参数是一个真正的数组，而 `arguments` 是一个类数组。  

    ```javascript
    // bad
    function concatenateAll() {
      const args = Array.prototype.slice.call(arguments);
      return args.join('');
    }
    
    // good
    function concatenateAll(...args) {
      return args.join('');
    }
    ```
7. 使用函数默认参数指定默认值，而不是用一个可变的函数参数

    ```javascript
    // really bad
    function handleThings(opts) {
      // 不！我们不应该改变函数参数
      // 更糟糕的是: 如果 opts 是 falsy (为''或者是false), 它仍然会被赋值为对象，但是这可能会引发bug
      opts = opts || {};
      // ...
    }
    
    // still bad
    function handleThings(opts) {
      if (opts === void 0) {
        opts = {};
      }
      // ...
    }
    
    // good
    function handleThings(opts = {}) {
      // ...
    }
    ```
8.使用函数参数默认值的时避免副作用。
    > 为什么？这样的写法会让人困惑。
    
    ```javascript
    var b = 1;
    // bad
    function count(a = b++) {
      console.log(a);
    }
    count();  // 1
    count();  // 2
    count(3); // 3
    count();  // 3
    ```
9. 参数默认值放在函数参数列表的最后。

    ```javascript
    // bad
    function handleThings(opts = {}, name) {
      // ...
    }
    
    // good
    function handleThings(name, opts = {}) {
      // ...
    }
    ```
10. 不要使用`Function`构造器创建函数。 eslint: [no-new-func](https://eslint.org/docs/rules/no-new-func)
    > 为什么？通过这种方式创建的函数和使用`eval()`类似，会带来不确定的问题

    ```javascript
    // bad
    var add = new Function('a', 'b', 'return a + b');
    
    // still bad
    var subtract = Function('a', 'b', 'return a - b');
    ```
11. 函数名两边留白。eslint: [space-before-function-paren](https://eslint.org/docs/rules/space-before-function-paren) [space-before-blocks]
    > 为什么？保持代码一致性，当你添加或者删除名字时不需要额外增减空格。

    ```javascript
    // bad
    const f = function(){};
    const g = function (){};
    const h = function() {};
    
    // good
    const x = function () {};
    const y = function a() {};
    ```
12. 不要修改参数。 eslint: [no-param-reassign](https://eslint.org/docs/rules/no-param-reassign.html)
    > 为什么？操作参数对象会在原始调用方中导致不可预知的变量副作用。

    ```javascript
    // bad
    function f1(obj) {
      obj.key = 1;
    }
    
    // good
    function f2(obj) {
      const key = Object.prototype.hasOwnProperty.call(obj, 'key') ? obj.key : 1;
    }
    ```
13. 不要给参数赋值。eslint: [no-param-reassign](https://eslint.org/docs/rules/no-param-reassign.html)
    > 为什么？重新分配参数可能会导致意外的行为，特别是在访问参数对象时。 它也可能导致优化问题，特别是在V8中。

    ```javascript
    // bad
    function f1(a) {
      a = 1;
      // ...
    }
    
    function f2(a) {
      if (!a) { a = 1; }
      // ...
    }
    
    // good
    function f3(a) {
      const b = a || 1;
      // ...
    }
    
    function f4(a = 1) {
      // ...
    }
    ```
14. 使用展开操作符`...`调用可变参数函数。eslint: [prefer-spread](https://eslint.org/docs/rules/prefer-spread)
    > 为什么？它更简洁，你不需要提供上下文，并且组合使用`new`和`apply`不容易。

    ```javascript
    // bad
    const x = [1, 2, 3, 4, 5];
    console.log.apply(console, x);
    
    // good
    const x = [1, 2, 3, 4, 5];
    console.log(...x);
    
    // bad
    new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));
    
    // good
    new Date(...[2016, 8, 5]);
    ```

15. 带有多行函数签名或调用的函数应该像本指南中的其他多行列表一样缩进：每行中包含一项，最后一个项目带有逗号。

    ```javascript
    // bad
    function foo(bar,
                 baz,
                 quux) {
      // ...
    }
    
    // good
    function foo(
      bar,
      baz,
      quux,
    ) {
      // ...
    }
    
    // bad
    console.log(foo,
      bar,
      baz);
    
    // good
    console.log(
      foo,
      bar,
      baz,
    );
    ```
## 箭头函数
1. 当你必须要使用匿名函数（如在传递内联回调时），请使用箭头函数。eslint: [prefer-arrow-callback](https://eslint.org/docs/rules/prefer-arrow-callback.html), [arrow-spacing](https://eslint.org/docs/rules/arrow-spacing.html) jscs: [requireArrowFunctions](http://jscs.info/rule/requireArrowFunctions)
    > 为什么?因为箭头函数创造了新的一个 this 执行环境，通常情况下都能满足你的需求，而且这样的写法更为简洁。（参考 [Arrow functions - JavaScript | MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions) ）

    > 为什么不？如果你有一个相当复杂的函数，你或许可以把逻辑部分转移到一个函数声明上。
    
    ```javascript
    // bad
    [1, 2, 3].map(function (x) {
      const y = x + 1;
      return x * y;
    });
    
    // good
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
    ```

2. 如果一个函数适合用一行写出并且只有一个参数，那就把花括号、圆括号和 return 都省略掉。如果不是，那就不要省略。eslint: [arrow-parens](https://eslint.org/docs/rules/arrow-parens.html), [arrow-body-style](https://eslint.org/docs/rules/arrow-body-style.html) jscs: [disallowParenthesesAroundArrowParam](http://jscs.info/rule/disallowParenthesesAroundArrowParam), [requireShorthandArrowFunctions](http://jscs.info/rule/requireShorthandArrowFunctions) 
    >  为什么？这是一个很好用的语法糖。在链式调用中可读性很高。

    ```javascript
    // bad
    [1, 2, 3].map(number => {
      const nextNumber = number + 1;
      `A string containing the ${nextNumber}.`;
    });
    
    // good
    [1, 2, 3].map(number => `A string containing the ${number}.`);
    
    // good
    [1, 2, 3].map((number) => {
      const nextNumber = number + 1;
      return `A string containing the ${nextNumber}.`;
    });
    
    // good
    [1, 2, 3].map((number, index) => ({
      [index]: number,
    }));
    
    // No implicit return with side effects
    function foo(callback) {
      const val = callback();
      if (val === true) {
        // Do something if callback returns true
      }
    }
    
    let bool = false;
    
    // bad
    foo(() => bool = true);
    
    // good
    foo(() => {
      bool = true;
    });
    ```

3. 如果表达式过长需要多行表示，请将其包含在括号中，增加可读性。
    > 为什么？它能清除的标识函数的开始和结束位置。

    ```javascript
    // bad
    ['get', 'post', 'put'].map(httpMethod => Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    );
    
    // good
    ['get', 'post', 'put'].map(httpMethod => (
      Object.prototype.hasOwnProperty.call(
        httpMagicObjectWithAVeryLongName,
        httpMethod,
      )
    ));
    ```
4. 如果函数只有一个参数并且函数体没有使用花括号，那就省略括号。否则，为了保持清晰一致性，总在参数周围加上括号。总是使用括号也是可以接受的，在这种情况下使用eslint的 [“always” option](https://eslint.org/docs/rules/arrow-parens#always) 或者不要在jscs中引入 [disallowParenthesesAroundArrowParam](http://jscs.info/rule/disallowParenthesesAroundArrowParam)。eslint: [arrow-parens](https://eslint.org/docs/rules/arrow-parens.html) jscs: [disallowParenthesesAroundArrowParam](http://jscs.info/rule/disallowParenthesesAroundArrowParam)
    > 为什么？ 不那么混乱，可读性强。

    ```javascript
    // bad
    [1, 2, 3].map((x) => x * x);
    
    // good
    [1, 2, 3].map(x => x * x);
    
    // good
    [1, 2, 3].map(number => (
      `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
    ));
    
    // bad
    [1, 2, 3].map(x => {
      const y = x + 1;
      return x * y;
    });
    
    // good
    [1, 2, 3].map((x) => {
      const y = x + 1;
      return x * y;
    });
    ```
5. 避免箭头函数语法（`=>`）和比较运算符（`<=`,`=>`）一起使用时带来的困惑。

    ```javascript
    // bad
    const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;
    
    // bad
    const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;
    
    // good
    const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);
    
    // good
    const itemHeight = (item) => {
      const { height, largeSize, smallSize } = item;
      return height > 256 ? largeSize : smallSize;
    };
    ```

## 类 & 构造函数
1. 总是使用`class`。避免直接操作`prototype`。
    > 为什么？`class`语法更简洁更易于理解。
    
    ```javascript
    // bad
    function Queue(contents = []) {
      this.queue = [...contents];
    }
    Queue.prototype.pop = function () {
      const value = this.queue[0];
      this.queue.splice(0, 1);
      return value;
    };
    
    // good
    class Queue {
      constructor(contents = []) {
        this.queue = [...contents];
      }
      pop() {
        const value = this.queue[0];
        this.queue.splice(0, 1);
        return value;
      }
    }
    ```
2. 使用`extends`继承。
    >  为什么？ 因为 extends 是一个内建的原型继承方法并且不会破坏 instanceof。

    ```javascript
    // bad
    const inherits = require('inherits');
    function PeekableQueue(contents) {
      Queue.apply(this, contents);
    }
    inherits(PeekableQueue, Queue);
    PeekableQueue.prototype.peek = function () {
      return this.queue[0];
    };
    
    // good
    class PeekableQueue extends Queue {
      peek() {
        return this.queue[0];
      }
    }
    
    ```
3. 方法可以返回 this 来帮助链式调用。

    ```javascript
    // bad
    Jedi.prototype.jump = function () {
      this.jumping = true;
      return true;
    };
    
    Jedi.prototype.setHeight = function (height) {
      this.height = height;
    };
    
    const luke = new Jedi();
    luke.jump(); // => true
    luke.setHeight(20); // => undefined
    
    // good
    class Jedi {
      jump() {
        this.jumping = true;
        return this;
      }
    
      setHeight(height) {
        this.height = height;
        return this;
      }
    }
    
    const luke = new Jedi();
    
    luke.jump()
      .setHeight(20);
    ```
4. 可以写一个自定义的 toString() 方法，但要确保它能正常运行并且不会引起副作用。

    ```javascript
    class Jedi {
      constructor(options = {}) {
        this.name = options.name || 'no name';
      }
    
      getName() {
        return this.name;
      }
    
      toString() {
        return `Jedi - ${this.getName()}`;
      }
    }
    ``` 
5. 类有默认构造器。一个空的构造函数或者只是重载父类构造函数是不必要的。eslint: [no-useless-constructor](https://eslint.org/docs/rules/no-useless-constructor)

    ```javascript
    // bad
    class Jedi {
      constructor() {}
    
      getName() {
        return this.name;
      }
    }
    
    // bad
    class Rey extends Jedi {
      constructor(...args) {
        super(...args);
      }
    }
    
    // good
    class Rey extends Jedi {
      constructor(...args) {
        super(...args);
        this.name = 'Rey';
      }
    }
    ```
6. 避免重复的类成员。eslint: [no-dupe-class-members](https://eslint.org/docs/rules/no-dupe-class-members) 
    > 为什么？重复的类成员声明中只有最后一个生效-重复的声明肯定是一个错误。

    ```javascript
    // bad
    class Foo {
      bar() { return 1; }
      bar() { return 2; }
    }
    
    // good
    class Foo {
      bar() { return 1; }
    }
    
    // good
    class Foo {
      bar() { return 2; }
    }
    
    ```

## 模块
1. 总是使用模组 (`import/export`) 而不是其他非标准模块系统。你可以编译为你喜欢的模块系统。
    > 为什么？模块是未来，让我们开始迈向未来吧。

    ```javascript
    // bad
    const AirbnbStyleGuide = require('./AirbnbStyleGuide');
    module.exports = AirbnbStyleGuide.es6;
    
    // ok
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    export default AirbnbStyleGuide.es6;
    
    // best
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```
2. 不要使用通配符  `import`
    > 为什么？这样确保只有一个默认的export

    ```javascript
    // bad
    import * as AirbnbStyleGuide from './AirbnbStyleGuide';
    
    // good
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    ```
3. 不要直接从`import`中`export`
    > 为什么？虽然一行代码简洁明了，但让 `import` 和 `export` 各司其职让事情能保持一致。
    
    ```javascript
    // bad
    // filename es6.js
    export { es6 as default } from './AirbnbStyleGuide';
    
    // good
    // filename es6.js
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```
4. 同一个路径只使用一次`import`。eslint: [no-duplicate-imports](https://eslint.org/docs/rules/no-duplicate-imports) 
    > 为什么？相同路径有多个`import`会导致代码难以维护。

    ```javascript
    // bad
    import foo from 'foo';
    // … some other imports … //
    import { named1, named2 } from 'foo';
    
    // good
    import foo, { named1, named2 } from 'foo';
    
    // good
    import foo, {
      named1,
      named2,
    } from 'foo';
    ```
5. 不要`export`可变的绑定。 eslint: [import/no-mutable-exports](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-mutable-exports.md)
    > 为什么？避免不确定的可变量，特别是`export`可变的绑定。如果某些特殊情况需要使用这种场景，通常应该`export`常量引用。

    ```javascript
    // bad
    let foo = 3;
    export { foo };
    
    // good
    const foo = 3;
    export { foo };
    ```
6. 模块中只有单个`export`,最好使用`default export` 。 eslint: [import/prefer-default-export](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/prefer-default-export.md)
    > 为什么？一个文件最好只做一件事，这样更具备可读性和可维护性。

    ```javascript
    // bad
    export function foo() {}
    
    // good
    export default function foo() {}
    ```
7. 将所有的`import`语句放在文件的顶部。eslint: [import/first](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/first.md)
    > 为什么？由于`import`s会被提升，最好保持它们在顶部以防出现不可预期的行为。

    ```javascript
    // bad
    import foo from 'foo';
    foo.init();
    
    import bar from 'bar';
    
    // good
    import foo from 'foo';
    import bar from 'bar';
    
    foo.init();
    ```
8. 多行`import`应该和多行数组和对象一样有缩进。
    > 为什么？花括号需要遵循与指南中的每个其他花括号相同的缩进规则，末尾的逗号也一样。

    ```javascript
    // bad
    import {longNameA, longNameB, longNameC, longNameD, longNameE} from 'path';
    
    // good
    import {
      longNameA,
      longNameB,
      longNameC,
      longNameD,
      longNameE,
    } from 'path';
    ```
9. 禁止在模块导入语句中使用Webpack加载器语法。eslint: [import/no-webpack-loader-syntax](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-webpack-loader-syntax.md) 
    > 为什么？在`import`中使用webpack 语法会将代码耦合进bundler中。推荐在`webpack.config.js`中配置loader 规则。

    ```javascript
    // bad
    import fooSass from 'css!sass!foo.scss';
    import barCss from 'style!css!bar.css';
    
    // good
    import fooSass from 'foo.scss';
    import barCss from 'bar.css';
    ```
## 迭代器和生成器
1. 不要使用迭代器。推荐使用JavaScript的高阶函数（例如 map,forEach）替代如`for-in`或者`for-of`语句。eslint: [no-iterator](https://eslint.org/docs/rules/no-iterator.html)  [no-restricted-syntax](https://eslint.org/docs/rules/no-restricted-syntax) 
    > 为什么？这加强了我们不变的规则。处理纯函数的回调值更易读，这比它带来的副作用更重要。

    > 使用`map()` / `every()` / `filter()` / `find()` / `findIndex()` / `reduce()` / `some()` / ... 方法变量数组，通过使用`Object.keys()` / `Object.values()` / `Object.entries()` 方法生成对象的键/值数组，遍历对象。
    
    ```javascript
    const numbers = [1, 2, 3, 4, 5];
    
    // bad
    let sum = 0;
    for (let num of numbers) {
      sum += num;
    }
    sum === 15;
    
    // good
    let sum = 0;
    numbers.forEach((num) => {
      sum += num;
    });
    sum === 15;
    
    // best (use the functional force)
    const sum = numbers.reduce((total, num) => total + num, 0);
    sum === 15;
    
    // bad
    const increasedByOne = [];
    for (let i = 0; i < numbers.length; i++) {
      increasedByOne.push(numbers[i] + 1);
    }
    
    // good
    const increasedByOne = [];
    numbers.forEach((num) => {
      increasedByOne.push(num + 1);
    });
    
    // best (keeping it functional)
    const increasedByOne = numbers.map(num => num + 1);
    ```
    
2. 现在还不要使用生成器
    > 为什么？现在还不能很好的转换成ES5
3. 如果你必须要使用生成器，或者不采纳我们的建议，请确保正确合适的函数签名。
    > 为什么？`function`和`*`是等价的关键字- `*` 不是`function`的修饰词，`function*`是一个独特的构造器，区别于`function`。

    ```javascript
    // bad
    function * foo() {
      // ...
    }
    
    // bad
    const bar = function * () {
      // ...
    };
    
    // bad
    const baz = function *() {
      // ...
    };
    
    // bad
    const quux = function*() {
      // ...
    };
    
    // bad
    function*foo() {
      // ...
    }
    
    // bad
    function *foo() {
      // ...
    }
    
    // very bad
    function
    *
    foo() {
      // ...
    }
    
    // very bad
    const wat = function
    *
    () {
      // ...
    };
    
    // good
    function* foo() {
      // ...
    }
    
    // good
    const foo = function* () {
      // ...
    };
    ```

## 属性
1. 使用`.`访问对象属性。eslint: [dot-notation](https://eslint.org/docs/rules/dot-notation.html) jscs: [requireDotNotation](http://jscs.info/rule/requireDotNotation)

    ```javascript
    const luke = {
      jedi: true,
      age: 28,
    };
    
    // bad
    const isJedi = luke['jedi'];
    
    // good
    const isJedi = luke.jedi;
    ```
2. 当通过变量访问属性时使用中括号 `[]`。

    ```javascript
    const luke = {
      jedi: true,
      age: 28,
    };
    
    function getProp(prop) {
      return luke[prop];
    }
    
    const isJedi = getProp('jedi');
    ```
3. 计算幂指数时使用指数运算符`**`。eslint: [no-restricted-properties](https://eslint.org/docs/rules/no-restricted-properties)

    ```javascript
    // bad
    const binary = Math.pow(2, 10);
    
    // good
    const binary = 2 ** 10;
    ```

## 变量
1. 一直使用 `const`或`let` 来声明变量，如果不这样做就会产生全局变量。我们需要避免全局命名空间的污染。地球队长已经警告过我们了。（全局，global 亦有全球的意思。地球队长的责任是保卫地球环境，所以他警告我们不要造成「全球」污染。）eslint: [no-undef](https://eslint.org/docs/rules/no-undef) [prefer-const](https://eslint.org/docs/rules/prefer-const)

    ```javascript
    // bad
    superPower = new SuperPower();
    
    // good
    const superPower = new SuperPower();
    ```
2. 使用`const`或`let`声明每一个变量。eslint: [one-var](https://eslint.org/docs/rules/one-var.html) jscs: [disallowMultipleVarDecl](http://jscs.info/rule/disallowMultipleVarDecl)
    > 为什么？这种方式添加新的变量变得更容易，而且新增变量的时候不用担心用错`;`或者`,`。此外，每个变量单独声明能够更方便地调试，而不是一次性地跳过所以的声明。

    ```javascript
    // bad
    const items = getItems(),
        goSportsTeam = true,
        dragonball = 'z';
    
    // bad
    // (compare to above, and try to spot the mistake)
    const items = getItems(),
        goSportsTeam = true;
        dragonball = 'z';
    
    // good
    const items = getItems();
    const goSportsTeam = true;
    const dragonball = 'z';
    ```
3. 分组声明`const`和`let`变量。
    > 为什么？后续再对变量赋值的时候就会很直观，哪些能够修改，哪些不能修改。

    ```javascript
    // bad
    let i, len, dragonball,
        items = getItems(),
        goSportsTeam = true;
    
    // bad
    let i;
    const items = getItems();
    let dragonball;
    const goSportsTeam = true;
    let len;
    
    // good
    const goSportsTeam = true;
    const items = getItems();
    let dragonball;
    let i;
    let length;
    ```
4. 在你需要的地方给变量赋值，但请把它们放在一个合理的位置。
    > 为什么？`let` 和 `const` 是块级作用域而不是函数作用域。

    ```javascript
    // bad - unnecessary function call
    function checkName(hasName) {
      const name = getName();
    
      if (hasName === 'test') {
        return false;
      }
    
      if (name === 'test') {
        this.setName('');
        return false;
      }
    
      return name;
    }
    
    // good
    function checkName(hasName) {
      if (hasName === 'test') {
        return false;
      }
    
      const name = getName();
    
      if (name === 'test') {
        this.setName('');
        return false;
      }
    
      return name;
    }
    ```
5. 不要链式的给变量赋值。 eslint: [no-multi-assign](https://eslint.org/docs/rules/no-multi-assign)
    > 为什么？链式赋值会创建隐含的全局变量。

    ```javascript
    // bad
    (function example() {
      // JavaScript 解释器 会将这段代码识别为
      // let a = ( b = ( c = 1 ) );
      // let 关键词只会作用于变量a，变量b和c会声明成全局变量 
      let a = b = c = 1;
    }());
    
    console.log(a); // throws ReferenceError
    console.log(b); // 1
    console.log(c); // 1
    
    // good
    (function example() {
      let a = 1;
      let b = a;
      let c = a;
    }());
    
    console.log(a); // throws ReferenceError
    console.log(b); // throws ReferenceError
    console.log(c); // throws ReferenceError
    
    //  `const` 声明也是同样的道理
    ```
6. 避免使用一元递增和递减操作（++，--）。eslint: [no-plusplus](https://eslint.org/docs/rules/no-plusplus)
    > 为什么？根据eslint的文档，一元递增或递减操作会受到自动插入分号的影响，并可能导致应用中的值递增或递减而没有错误提示。使用像`num += 1`而不是`num++` 或 `num ++`语句来改变值也更加直观。禁止一元递增或递减语句也会帮助你减少无意中预先的值增减，降低应用出错的风险。

    ```javascript
    // bad
    
    const array = [1, 2, 3];
    let num = 1;
    num++;
    --num;
    
    let sum = 0;
    let truthyCount = 0;
    for (let i = 0; i < array.length; i++) {
      let value = array[i];
      sum += value;
      if (value) {
        truthyCount++;
      }
    }
    
    // good
    
    const array = [1, 2, 3];
    let num = 1;
    num += 1;
    num -= 1;
    
    const sum = array.reduce((a, b) => a + b, 0);
    const truthyCount = array.filter(Boolean).length;
    ```
7. 赋值语句中避免在`=`后立即换行。如果赋值超出了 [max-len](https://eslint.org/docs/rules/max-len.html) 规则，需要用括号将值包裹起来。 eslint: [operator-linebreak](https://eslint.org/docs/rules/operator-linebreak.html) 
    > 为什么？`=`后的换行让代码的赋值变得不直观。
    
    ```javascript
    // bad
    const foo =
      superLongLongLongLongLongLongLongLongFunctionName();
    
    // bad
    const foo
      = 'superLongLongLongLongLongLongLongLongString';
    
    // good
    const foo = (
      superLongLongLongLongLongLongLongLongFunctionName()
    );
    
    // good
    const foo = 'superLongLongLongLongLongLongLongLongString';
    ```

## 变量提升
1. `var` 声明会被提升至该作用域的顶部，但它们赋值不会提升。`let` 和 `const` 被赋予了一种称为[「暂时性死区（Temporal Dead Zones, TDZ）」](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/let#Temporal_Dead_Zone_and_errors_with_let)的概念。这对于了解为什么 [type of 不再安全](http://es-discourse.com/t/why-typeof-is-no-longer-safe/15)相当重要。

    ```javascript
    // 我们知道这样运行不了
    // （假设 notDefined 不是全局变量）
    function example() {
      console.log(notDefined); // => throws a ReferenceError
    }
    
    // 由于变量提升的原因，
    // 在引用变量后再声明变量是可以运行的。
    // 注：变量的赋值 `true` 不会被提升。
    function example() {
      console.log(declaredButNotAssigned); // => undefined
      var declaredButNotAssigned = true;
    }
    
    // 编译器会把函数声明提升到作用域的顶层，
    // 这意味着我们的例子可以改写成这样：
    function example() {
      let declaredButNotAssigned;
      console.log(declaredButNotAssigned); // => undefined
      declaredButNotAssigned = true;
    }
    
    // 使用 const 和 let
    function example() {
      console.log(declaredButNotAssigned); // => throws a ReferenceError
      console.log(typeof declaredButNotAssigned); // => throws a ReferenceError
      const declaredButNotAssigned = true;
    }
    ```
2. 匿名函数表达式的变量名会被提升，但函数内容并不会。

    ```javascript
    function example() {
      console.log(anonymous); // => undefined
    
      anonymous(); // => TypeError anonymous is not a function
    
      var anonymous = function () {
        console.log('anonymous function expression');
      };
    }
    ```
3. 命名的函数表达式的变量名会被提升，但函数名和函数函数内容并不会。

    ```javascript
    function example() {
      console.log(named); // => undefined
    
      named(); // => TypeError named is not a function
    
      superPower(); // => ReferenceError superPower is not defined
    
      var named = function superPower() {
        console.log('Flying');
      };
    }
    
    // 函数名和变量名相同时，结果也是一样的
    function example() {
      console.log(named); // => undefined
    
      named(); // => TypeError named is not a function
    
      var named = function named() {
        console.log('named');
      };
    }
    ```
4. 函数声明的名称和函数体都会被提升。

    ```javascript
    function example() {
      superPower(); // => Flying
    
      function superPower() {
        console.log('Flying');
      }
    }
    ```
- 想了解更多信息，参考 [Ben Cherry](http://www.adequatelygood.com/) 的 [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting/)。

## 比较运算符&等号
1. 使用`===` 和`!==`而不是`==`和`!=`。eslint: [eqeqeq](https://eslint.org/docs/rules/eqeqeq.html)
2. 条件表达式例如 `if` 语句通过抽象方法 `ToBoolean` 强制计算它们的表达式并且总是遵守下面的规则：
    - 对象 被计算为 true
    - Undefined 被计算为 false
    - Null 被计算为 false
    - 布尔值 被计算为 布尔的值
    - 数字 如果是 +0、-0、或 NaN 被计算为 false, 否则为 true
    - 字符串 如果是空字符串 '' 被计算为 false，否则为 true
    
    ```javascript
    if ([0] && []) {
      // true
      // 数组是对象 (即使是空数组), 对象总是为true
    }
    ```
3. 使用简写，但是string和number类型需要显式比较。

    ```javascript
    // bad
    if (isValid === true) {
      // ...
    }
    
    // good
    if (isValid) {
      // ...
    }
    
    // bad
    if (name) {
      // ...
    }
    
    // good
    if (name !== '') {
      // ...
    }
    
    // bad
    if (collection.length) {
      // ...
    }
    
    // good
    if (collection.length > 0) {
      // ...
    }
    ```
4. 想了解更多信息，参考 Angus Croll 的 [Truth Equality and JavaScript](https://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108)。
5. `case`和`default`代码块中如果有声明语句（例如`let`,`const`,`function`和`class`），使用花括号包裹起来。eslint: [no-case-declarations](https://eslint.org/docs/rules/no-case-declarations.html) 
    > 为什么？词法声明会在整个`switch`代码块中都可见，但是只在`case`中的赋时候初始化。如果多个`case`代码块中定义相同的内容时就会有问题。

    ```javascript
    // bad
    switch (foo) {
      case 1:
        let x = 1;
        break;
      case 2:
        const y = 2;
        break;
      case 3:
        function f() {
          // ...
        }
        break;
      default:
        class C {}
    }
    
    // good
    switch (foo) {
      case 1: {
        let x = 1;
        break;
      }
      case 2: {
        const y = 2;
        break;
      }
      case 3: {
        function f() {
          // ...
        }
        break;
      }
      case 4:
        bar();
        break;
      default: {
        class C {}
      }
    }
    ```

6. 三元操作不应该嵌套，并且最好能够写成单行语句。 eslint: [no-nested-ternary](https://eslint.org/docs/rules/no-nested-ternary.html) 

    ```javascript
    const foo = maybe1 > maybe2
      ? "bar"
      : value1 > value2 ? "baz" : null;
    
    // split into 2 separated ternary expressions
    const maybeNull = value1 > value2 ? 'baz' : null;
    
    // better
    const foo = maybe1 > maybe2
      ? 'bar'
      : maybeNull;
    
    // best
    const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
    ```
    
7. 避免不必要的三元语句。 eslint: [no-unneeded-ternary](https://eslint.org/docs/rules/no-unneeded-ternary.html) 

    ```javascript
    // bad
    const foo = a ? a : b;
    const bar = c ? true : false;
    const baz = c ? false : true;
    
    // good
    const foo = a || b;
    const bar = !!c;
    const baz = !c;
    ```
8. 当混合使用多个操作符或者运算符时，最好将他们使用括号包裹起来。唯一的例外是基本的算术运算符（`+`，`-`，`*`，`/`）,因为它们的运算优先级广为人知。 eslint: [no-mixed-operators](https://eslint.org/docs/rules/no-mixed-operators.html) 
    > 为什么？更具有可读性。

    ```javascript
    // bad
    const foo = a && b < 0 || c > 0 || d + 1 === 0;
    
    // bad
    const bar = a ** b - 5 % d;
    
    // bad
    // one may be confused into thinking (a || b) && c
    if (a || b && c) {
      return d;
    }
    
    // good
    const foo = (a && b < 0) || c > 0 || (d + 1 === 0);
    
    // good
    const bar = (a ** b) - (5 % d);
    
    // good
    if (a || (b && c)) {
      return d;
    }
    
    // good
    const bar = a + b / c * d;
    ```

## 代码块
1. 使用大括号包裹所有的多行代码块。eslint: [nonblock-statement-body-position](https://eslint.org/docs/rules/nonblock-statement-body-position)

    ```javascript
    // bad
    if (test)
      return false;
    
    // good
    if (test) return false;
    
    // good
    if (test) {
      return false;
    }
    
    // bad
    function foo() { return false; }
    
    // good
    function bar() {
      return false;
    }
    ```
2. 如果通过 `if` 和 `else` 使用多行代码块，把 `else` 放在 `if` 代码块关闭括号的同一行。eslint: [brace-style](https://eslint.org/docs/rules/brace-style.html) jscs: [disallowNewlineBeforeBlockStatements](http://jscs.info/rule/disallowNewlineBeforeBlockStatements)

    ```javascript
    // bad
    if (test) {
      thing1();
      thing2();
    }
    else {
      thing3();
    }
    
    // good
    if (test) {
      thing1();
      thing2();
    } else {
      thing3();
    }
    ```

3. 如果`if`代码块一定会有`return`语句,后续的`else`语句是没必要的。如果`if`和`else if`代码块都包含`return`语句，则可以拆分成多个`if`代码块。eslint: [no-else-return](https://eslint.org/docs/rules/no-else-return) 

    ```javascript
    // bad
    function foo() {
      if (x) {
        return x;
      } else {
        return y;
      }
    }
    
    // bad
    function cats() {
      if (x) {
        return x;
      } else if (y) {
        return y;
      }
    }
    
    // bad
    function dogs() {
      if (x) {
        return x;
      } else {
        if (y) {
          return y;
        }
      }
    }
    
    // good
    function foo() {
      if (x) {
        return x;
      }
    
      return y;
    }
    
    // good
    function cats() {
      if (x) {
        return x;
      }
    
      if (y) {
        return y;
      }
    }
    
    //good
    function dogs(x) {
      if (x) {
        if (z) {
          return y;
        }
      } else {
        return z;
      }
    }
    ```

## 控制语句
1. 如果控制语句（`if`，`while`等）中的判断条件太长或者超过最大的行长度时，每个条件判断单独一行，并以逻辑操作符作为行开始。
    > 为什么？要求操作符在行首并对其保持了指南一致性，并且也让复杂的逻辑判断易于阅读和理解。

    ```javascript
    // bad
    if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
      thing1();
    }
    
    // bad
    if (foo === 123 &&
      bar === 'abc') {
      thing1();
    }
    
    // bad
    if (foo === 123
      && bar === 'abc') {
      thing1();
    }
    
    // bad
    if (
      foo === 123 &&
      bar === 'abc'
    ) {
      thing1();
    }
    
    // good
    if (
      foo === 123
      && bar === 'abc'
    ) {
      thing1();
    }
    
    // good
    if (
      (foo === 123 || bar === "abc")
      && doesItLookGoodWhenItBecomesThatLong()
      && isThisReallyHappening()
    ) {
      thing1();
    }
    
    // good
    if (foo === 123 && bar === 'abc') {
      thing1();
    }
    ```

## 注释
1. 使用`/** ... */`注释多行。
    
    ```javascript
    // bad
    // make() returns a new element
    // based on the passed in tag name
    //
    // @param {String} tag
    // @return {Element} element
    function make(tag) {
    
      // ...
    
      return element;
    }
    
    // good
    /**
     * make() returns a new element
     * based on the passed-in tag name
     */
    function make(tag) {
    
      // ...
    
      return element;
    }
    ```

2. 使用`//`注释单行。在注释的主体上另起一行进行单行注释。除非注释在代码块的第一行，否则注释前面留一个空行。

    ```javascript
    // bad
    const active = true;  // is current tab
    
    // good
    // is current tab
    const active = true;
    
    // bad
    function getType() {
      console.log('fetching type...');
      // set the default type to 'no type'
      const type = this.type || 'no type';
    
      return type;
    }
    
    // good
    function getType() {
      console.log('fetching type...');
    
      // set the default type to 'no type'
      const type = this.type || 'no type';
    
      return type;
    }
    
    // also good
    function getType() {
      // set the default type to 'no type'
      const type = this.type || 'no type';
    
      return type;
    }
    ```
    
3. 所有的注释文本前留一个空格，易于阅读。 eslint: [spaced-comment](https://eslint.org/docs/rules/spaced-comment)

    ```javascript
    // bad
    //is current tab
    const active = true;
    
    // good
    // is current tab
    const active = true;
    
    // bad
    /**
     *make() returns a new element
     *based on the passed-in tag name
     */
    function make(tag) {
    
      // ...
    
      return element;
    }
    
    // good
    /**
     * make() returns a new element
     * based on the passed-in tag name
     */
    function make(tag) {
    
      // ...
    
      return element;
    }
    ```
4. 给注释增加 `FIXME` 或 `TODO` 的前缀可以帮助其他开发者快速了解这是一个需要复查的问题，或是给需要实现的功能提供一个解决方式。这将有别于常见的注释，因为它们是可操作的。使用 `FIXME -- need to figure this out` 或者 ` -- need to implement`。
5. 使用 `// FIXME:` 标注问题

    ```javascript
    class Calculator extends Abacus {
      constructor() {
        super();
    
        // FIXME: shouldn’t use a global here
        total = 0;
      }
    }
    ```
6. 使用 `// TODO:` 标注问题的解决方式。

    ```javascript
    class Calculator extends Abacus {
      constructor() {
        super();
    
        // TODO: total should be configurable by an options param
        this.total = 0;
      }
    }
    ```

## 空白
1. 使用 2 个空格作为缩进。eslint: [indent](https://eslint.org/docs/rules/indent.html) jscs: [validateIndentation](http://jscs.info/rule/validateIndentation)

    ```javascript
    // bad
    function foo() {
    ∙∙∙∙let name;
    }
    
    // bad
    function bar() {
    ∙let name;
    }
    
    // good
    function baz() {
    ∙∙let name;
    }
    ```
2. 在花括号前放一个空格。 eslint: [space-before-blocks](https://eslint.org/docs/rules/space-before-blocks.html) jscs: [requireSpaceBeforeBlockStatements](http://jscs.info/rule/requireSpaceBeforeBlockStatements) 

    ```javascript
    // bad
    function test(){
      console.log('test');
    }
    
    // good
    function test() {
      console.log('test');
    }
    
    // bad
    dog.set('attr',{
      age: '1 year',
      breed: 'Bernese Mountain Dog',
    });
    
    // good
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog',
    });
    ```
3. 在控制语句（`if`、`while` 等）的小括号前放一个空格。在函数调用及声明中，不在函数的参数列表前加空格。eslint: [keyword-spacing](https://eslint.org/docs/rules/keyword-spacing.html) jscs: [requireSpaceAfterKeywords](http://jscs.info/rule/requireSpaceAfterKeywords)

    ```javascript
    // bad
    if(isJedi) {
      fight ();
    }
    
    // good
    if (isJedi) {
      fight();
    }
    
    // bad
    function fight () {
      console.log ('Swooosh!');
    }
    
    // good
    function fight() {
      console.log('Swooosh!');
    }
    ```
4. 使用空格把运算符隔开。 eslint: [space-infix-ops](https://eslint.org/docs/rules/space-infix-ops.html) jscs: [requireSpaceBeforeBinaryOperators](http://jscs.info/rule/requireSpaceBeforeBinaryOperators) [requireSpaceAfterBinaryOperators](http://jscs.info/rule/requireSpaceAfterBinaryOperators) 

    ```javascript
    // bad
    const x=y+5;
    
    // good
    const x = y + 5;
    ```
5. 在文件末尾插入一个空行。eslint: [eol-last](https://github.com/eslint/eslint/blob/master/docs/rules/eol-last.md) 

    ```javascript
    // bad
    import { es6 } from './AirbnbStyleGuide';
      // ...
    export default es6;
    ```
    
    ```javascript
    // bad
    import { es6 } from './AirbnbStyleGuide';
      // ...
    export default es6;↵
    ↵
    ```

    ```javascript
    // good
    import { es6 } from './AirbnbStyleGuide';
      // ...
    export default es6;↵
    ```
6. 在长的链式方法调用时使用缩进（超过2个链式方法）。以`.`作为行开头，强调这是一个方法调用而不是新的语句。 eslint: [newline-per-chained-call](https://eslint.org/docs/rules/newline-per-chained-call) [no-whitespace-before-property](https://eslint.org/docs/rules/no-whitespace-before-property) 

    ```javascript
    // bad
    $('#items').find('.selected').highlight().end().find('.open').updateCount();
    
    // bad
    $('#items').
      find('.selected').
        highlight().
        end().
      find('.open').
        updateCount();
    
    // good
    $('#items')
      .find('.selected')
        .highlight()
        .end()
      .find('.open')
        .updateCount();
    
    // bad
    const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
        .attr('width', (radius + margin) * 2).append('svg:g')
        .attr('transform', `translate(${radius + margin},${radius + margin})`)
        .call(tron.led);
    
    // good
    const leds = stage.selectAll('.led')
        .data(data)
      .enter().append('svg:svg')
        .classed('led', true)
        .attr('width', (radius + margin) * 2)
      .append('svg:g')
        .attr('transform', `translate(${radius + margin},${radius + margin})`)
        .call(tron.led);
    
    // good
    const leds = stage.selectAll('.led').data(data);
    ```
7. 在代码块之后和下一个语句之前留空行。jscs: [requirePaddingNewLinesAfterBlocks](http://jscs.info/rule/requirePaddingNewLinesAfterBlocks) 

    ```javascript
    // bad
    if (foo) {
      return bar;
    }
    return baz;
    
    // good
    if (foo) {
      return bar;
    }
    
    return baz;
    
    // bad
    const obj = {
      foo() {
      },
      bar() {
      },
    };
    return obj;
    
    // good
    const obj = {
      foo() {
      },
    
      bar() {
      },
    };
    
    return obj;
    
    // bad
    const arr = [
      function foo() {
      },
      function bar() {
      },
    ];
    return arr;
    
    // good
    const arr = [
      function foo() {
      },
    
      function bar() {
      },
    ];
    
    return arr;
    ```
8. 代码块中不要有空行。 eslint: [padded-blocks](https://eslint.org/docs/rules/padded-blocks.html) jscs: [disallowPaddingNewlinesInBlocks](http://jscs.info/rule/disallowPaddingNewlinesInBlocks) 

    ```javascript
    // bad
    function bar() {
    
      console.log(foo);
    
    }
    
    // bad
    if (baz) {
    
      console.log(qux);
    } else {
      console.log(foo);
    
    }
    
    // bad
    class Foo {
    
      constructor(bar) {
        this.bar = bar;
      }
    }
    
    // good
    function bar() {
      console.log(foo);
    }
    
    // good
    if (baz) {
      console.log(qux);
    } else {
      console.log(foo);
    }
    ```
9. 不要在圆括号内加空格。 eslint: [space-in-parens](https://eslint.org/docs/rules/space-in-parens.html) jscs: [disallowSpacesInsideParentheses](http://jscs.info/rule/disallowSpacesInsideParentheses) 

    ```javascript
    // bad
    function bar( foo ) {
      return foo;
    }
    
    // good
    function bar(foo) {
      return foo;
    }
    
    // bad
    if ( foo ) {
      console.log(foo);
    }
    
    // good
    if (foo) {
      console.log(foo);
    }
    ```
10. 不要在方括号内加空格。 eslint: [array-bracket-spacing](https://eslint.org/docs/rules/array-bracket-spacing.html) jscs: [disallowSpacesInsideArrayBrackets](http://jscs.info/rule/disallowSpacesInsideArrayBrackets)

    ```javascript
    // bad
    const foo = [ 1, 2, 3 ];
    console.log(foo[ 0 ]);
    
    // good
    const foo = [1, 2, 3];
    console.log(foo[0]);
    ```
11. 大括号（花括号）内部添加空格。 eslint: [object-curly-spacing](https://eslint.org/docs/rules/object-curly-spacing.html) jscs: [requireSpacesInsideObjectBrackets](http://jscs.info/rule/requireSpacesInsideObjectBrackets)

    ```javascript
    // bad
    const foo = {clark: 'kent'};
    
    // good
    const foo = { clark: 'kent' };
    ```
12. 避免有超过100个字符（包括空格）的代码行。 注意：根据上面的规定，长字符串可以免除这个规则，不应该被折行。
    > 为什么？确保可读性和可维护性。

    ```javascript
    // bad
    const foo = jsonData && jsonData.foo && jsonData.foo.bar && jsonData.foo.bar.baz && jsonData.foo.bar.baz.quux && jsonData.foo.bar.baz.quux.xyzzy;
    
    // bad
    $.ajax({ method: 'POST', url: 'https://airbnb.com/', data: { name: 'John' } }).done(() => console.log('Congratulations!')).fail(() => console.log('You have failed this city.'));
    
    // good
    const foo = jsonData
      && jsonData.foo
      && jsonData.foo.bar
      && jsonData.foo.bar.baz
      && jsonData.foo.bar.baz.quux
      && jsonData.foo.bar.baz.quux.xyzzy;
    
    // good
    $.ajax({
      method: 'POST',
      url: 'https://airbnb.com/',
      data: { name: 'John' },
    })
      .done(() => console.log('Congratulations!'))
      .fail(() => console.log('You have failed this city.'));
    ```

## 逗号
1. 行首逗号：**不需要**。 eslint: [comma-style](https://eslint.org/docs/rules/comma-style.html) jscs: [requireCommaBeforeLineBreak](http://jscs.info/rule/requireCommaBeforeLineBreak)

    ```javascript
    // bad
    const story = [
        once
      , upon
      , aTime
    ];
    
    // good
    const story = [
      once,
      upon,
      aTime,
    ];
    
    // bad
    const hero = {
        firstName: 'Ada'
      , lastName: 'Lovelace'
      , birthYear: 1815
      , superPower: 'computers'
    };
    
    // good
    const hero = {
      firstName: 'Ada',
      lastName: 'Lovelace',
      birthYear: 1815,
      superPower: 'computers',
    };
    ```
2. 额外的尾部逗号：**正确**。eslint: [comma-dangle](https://eslint.org/docs/rules/comma-dangle.html) jscs: [requireTrailingComma](http://jscs.info/rule/requireTrailingComma)
    > 为什么？这样在git中代码diff 更加直观。另外，诸如Babel这类的转换器在转换过程中会删除末尾逗号，这样不用担心老版本浏览器的兼容问题。

    ```diff
    // bad - git diff without trailing comma
    const hero = {
         firstName: 'Florence',
    -    lastName: 'Nightingale'
    +    lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing']
    };
    
    // good - git diff with trailing comma
    const hero = {
         firstName: 'Florence',
         lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing'],
    };
    ```
    
    ```javascript
        // bad
    const hero = {
      firstName: 'Dana',
      lastName: 'Scully'
    };
    
    const heroes = [
      'Batman',
      'Superman'
    ];
    
    // good
    const hero = {
      firstName: 'Dana',
      lastName: 'Scully',
    };
    
    const heroes = [
      'Batman',
      'Superman',
    ];
    
    // bad
    function createHero(
      firstName,
      lastName,
      inventorOf
    ) {
      // does nothing
    }
    
    // good
    function createHero(
      firstName,
      lastName,
      inventorOf,
    ) {
      // does nothing
    }
    
    // good (注意：剩余参数后面就不需要带逗号了)
    function createHero(
      firstName,
      lastName,
      inventorOf,
      ...heroArgs
    ) {
      // does nothing
    }
    
    // bad
    createHero(
      firstName,
      lastName,
      inventorOf
    );
    
    // good
    createHero(
      firstName,
      lastName,
      inventorOf,
    );
    
    // good (注意：剩余参数后面就不需要带逗号了)
    createHero(
      firstName,
      lastName,
      inventorOf,
      ...heroArgs
    );
     ```

## 分号
1. 使用分号。 eslint: [semi](https://eslint.org/docs/rules/semi.html) jscs: [requireSemicolons](http://jscs.info/rule/requireSemicolons)
    > 为什么？当JavaScript 遇到没有分号的换行符时，它会使用自动分号插入规则（ASI）来确定是否将该换行符认为是语句的结尾，并且如果符合规则，将自动插入分号。但是ASI会有一些意外的行为，如果JavaScript错误的解释了换行符，代码就会中断。随着JavaScript增加更多的新功能，ASI的规则会变得越来越复杂。明确的标识语句的结束并且使用linter来辅助检查缺少的分号，可以有助于防止遇到意外的问题。

    ```javascript
    // bad - raises exception
    const luke = {}
    const leia = {}
    [luke, leia].forEach(jedi => jedi.father = 'vader')
    
    // bad - raises exception
    const reaction = "No! That's impossible!"
    (async function meanwhileOnTheFalcon(){
      // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
      // ...
    }())
    
    // bad - returns `undefined` instead of the value on the next line - always happens when `return` is on a line by itself because of ASI!
    function foo() {
      return
        'search your feelings, you know it to be foo'
    }
    
    // good
    const luke = {};
    const leia = {};
    [luke, leia].forEach((jedi) => {
      jedi.father = 'vader';
    });
    
    // good
    const reaction = "No! That's impossible!";
    (async function meanwhileOnTheFalcon(){
      // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
      // ...
    }());
    
    // good
    function foo() {
      return 'search your feelings, you know it to be foo';
    }
    ```
[查看更多示例](https://stackoverflow.com/questions/7365172/semicolon-before-self-invoking-function/7365214#7365214)

## 类型转换
1. 在语句开始时执行类型转换。
2. 字符串。eslint: [no-new-wrappers](https://eslint.org/docs/rules/no-new-wrappers)

    ```javascript
    // => this.reviewScore = 9;
    
    // bad
    const totalScore = new String(this.reviewScore); // typeof totalScore is "object" not "string"
    
    // bad
    const totalScore = this.reviewScore + ''; // invokes this.reviewScore.valueOf()
    
    // bad
    const totalScore = this.reviewScore.toString(); // isn’t guaranteed to return a string
    
    // good
    const totalScore = String(this.reviewScore);
    ```
3. 数字。使用`Number`进行类型转换，使用`parseInt`时带上转换基数。 eslint: [no-new-wrappers](https://eslint.org/docs/rules/no-new-wrappers)

    ```javascript
    const inputValue = '4';
    
    // bad
    const val = new Number(inputValue);
    
    // bad
    const val = +inputValue;
    
    // bad
    const val = inputValue >> 0;
    
    // bad
    const val = parseInt(inputValue);
    
    // good
    const val = Number(inputValue);
    
    // good
    const val = parseInt(inputValue, 10);
    ```
4. 如果因为某些原因 parseInt 成为你所做的事的瓶颈而需要使用位操作解决[性能问题](https://jsperf.com/coercion-vs-casting/3)时，留个注释说清楚原因和你的目的。

    ```javascript
    // good
    /**
     * 使用 parseInt 导致我的程序变慢，
     * 改成使用位操作转换数字快多了。
     */
    const val = inputValue >> 0;
    ```
5. 注: 小心使用位操作运算符。数字会被当成 [64 位值](https://es5.github.io/#x4.3.19)，但是位操作运算符总是返回 32 位的整数（[参考](https://es5.github.io/#x11.7)）。位操作处理大于 32 位的整数值时还会导致意料之外的行为。[关于这个问题的讨论](https://github.com/airbnb/javascript/issues/109)。最大的 32 位整数是 2,147,483,647：

    ```javascript
    2147483647 >> 0; // => 2147483647
    2147483648 >> 0; // => -2147483648
    2147483649 >> 0; // => -2147483647
    ```
6. 布尔值。eslint: [no-new-wrappers](https://eslint.org/docs/rules/no-new-wrappers)

    ```javascript
    const age = 0;
    
    // bad
    const hasAge = new Boolean(age);
    
    // good
    const hasAge = Boolean(age);
    
    // best
    const hasAge = !!age;
    ```
## 命名规则
1. 避免单字母命名。命名应具备描述性。eslint: [id-length](https://eslint.org/docs/rules/id-length)

    ```javascript
    // bad
    function q() {
      // ...
    }
    
    // good
    function query() {
      // ...
    }
    ```

2. 使用驼峰式命名对象、函数和实例。eslint: [camelcase](https://eslint.org/docs/rules/camelcase.html) jscs: [requireCamelCaseOrUpperCaseIdentifiers](http://jscs.info/rule/requireCamelCaseOrUpperCaseIdentifiers)

    ```javascript
    // bad
    const OBJEcttsssss = {};
    const this_is_my_object = {};
    function c() {}
    
    // good
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```

3.  使用帕斯卡式命名构造函数或类。eslint: [new-cap](https://eslint.org/docs/rules/new-cap.html) jscs: [requireCapitalizedConstructors](http://jscs.info/rule/requireCapitalizedConstructors)

    ```javascript
    // bad
    function user(options) {
      this.name = options.name;
    }
    
    const bad = new user({
      name: 'nope',
    });
    
    // good
    class User {
      constructor(options) {
        this.name = options.name;
      }
    }
    
    const good = new User({
      name: 'yup',
    });
    ```
4. 不要使用下划线 `_` 结尾或开头来命名属性和方法。eslint: [no-underscore-dangle](https://eslint.org/docs/rules/no-underscore-dangle.html) jscs: [disallowDanglingUnderscores](http://jscs.info/rule/disallowDanglingUnderscores) 
    > 为什么？JavaScript 没有私有属性或方法的概念。虽然以下划线开头是“私有的”的编码惯例，事实上这些属性是完全公开的，因此它们是你暴露的公共API的一部分。这样的约定会让开发人员错误地认为修改“私有”属性/方法不会有副作用，也不需要测试。泰勒博士：如果你想要一些东西是“私有的”，那它就不能显示的访问。

    ```javascript
    // bad
    this.__firstName__ = 'Panda';
    this.firstName_ = 'Panda';
    this._firstName = 'Panda';
    
    // good
    this.firstName = 'Panda';
    ```
5. 别保存 `this` 的引用。使用箭头函数或 [Function#bind](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)。jscs: [disallowNodeTypes](http://jscs.info/rule/disallowNodeTypes)

    ```javascript
    // bad
    function foo() {
      const self = this;
      return function () {
        console.log(self);
      };
    }
    
    // bad
    function foo() {
      const that = this;
      return function () {
        console.log(that);
      };
    }
    
    // good
    function foo() {
      return () => {
        console.log(this);
      };
    }
    ```
6. 如果你的文件只输出一个类，那你的文件名必须和类名完全保持一致。

    ```javascript
    // file 1 contents
    class CheckBox {
      // ...
    }
    export default CheckBox;
    
    // file 2 contents
    export default function fortyTwo() { return 42; }
    
    // file 3 contents
    export default function insideDirectory() {}
    
    // in some other file
    // bad
    import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
    import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
    import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export
    
    // bad
    import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
    import forty_two from './forty_two'; // snake_case import/filename, camelCase export
    import inside_directory from './inside_directory'; // snake_case import, camelCase export
    import index from './inside_directory/index'; // requiring the index file explicitly
    import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly
    
    // good
    import CheckBox from './CheckBox'; // PascalCase export/import/filename
    import fortyTwo from './fortyTwo'; // camelCase export/import/filename
    import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
    // ^ supports both insideDirectory.js and insideDirectory/index.js
    ```
7. 当你导出默认的函数时使用驼峰式命名。你的文件名必须和函数名完全保持一致。

    ```javascript
    function makeStyleGuide() {
      // ...
    }
    
    export default makeStyleGuide;
    ```
8. 当你导出构造器、类、单例、函数库、纯对象时使用帕斯卡式命名。

    ```javascript
    const AirbnbStyleGuide = {
      es6: {
      },
    };
    
    export default AirbnbStyleGuide;
    ```
9. 首字母缩略词和首字母缩写应该总是全部大写，或全部小写。
    > 为什么？名字应该具有可读性，而不是为了机器识别。

    ```javascript
    // bad
    import SmsContainer from './containers/SmsContainer';
    
    // bad
    const HttpRequests = [
      // ...
    ];
    
    // good
    import SMSContainer from './containers/SMSContainer';
    
    // good
    const HTTPRequests = [
      // ...
    ];
    
    // also good
    const httpRequests = [
      // ...
    ];
    
    // best
    import TextMessageContainer from './containers/TextMessageContainer';
    
    // best
    const requests = [
      // ...
    ];
    ```
## 存取器
1. 属性的存取函数不是必须的。
2. 不要使用JavaScript 的`getter`/`setter`，因为它们会导致意想不到的副作用，而且很难测试，维护和调试。相反，如果你想做访问函数，使用`getVal()`和`setVal('hello')`

    ```javascript
    // bad
    class Dragon {
      get age() {
        // ...
      }
    
      set age(value) {
        // ...
      }
    }
    
    // good
    class Dragon {
      getAge() {
        // ...
      }
    
      setAge(value) {
        // ...
      }
    }
    ```
3. 如果属性值/方法返回值是`boolean`类型，使用`isVal()`或者`hasVal()`。

    ```javascript
    // bad
    if (!dragon.age()) {
      return false;
    }
    
    // good
    if (!dragon.hasAge()) {
      return false;
    }
    ```
4. 创建 `get()` 和 `set()` 函数是可以的，但要保持一致。

    ```javascript
    class Jedi {
      constructor(options = {}) {
        const lightsaber = options.lightsaber || 'blue';
        this.set('lightsaber', lightsaber);
      }
    
      set(key, val) {
        this[key] = val;
      }
    
      get(key) {
        return this[key];
      }
    }
    ```
## 事件
1. 当给事件附加数据时（无论是 DOM 事件还是私有事件），传入一个哈希而不是原始值。这样可以让后面的贡献者增加更多数据到事件数据而无需找出并更新事件的每一个处理器。例如，不好的写法：

    ```javascript
    // bad
    $(this).trigger('listingUpdated', listing.id);
    ...
    $(this).on('listingUpdated', function(e, listingId) {
      // do something with listingId
    });
    ```
    
    更好的写法：

    ```javascript
    // good
    $(this).trigger('listingUpdated', { listingId : listing.id });
    ...
    $(this).on('listingUpdated', function(e, data) {
      // do something with data.listingId
    });
    ```
    
## jQuery
1.  使用 $ 作为存储 jQuery 对象的变量名前缀。jscs: [requireDollarBeforejQueryAssignment](http://jscs.info/rule/requireDollarBeforejQueryAssignment) 

    ```javascript
    // bad
    const sidebar = $('.sidebar');
    
    // good
    const $sidebar = $('.sidebar');
    
    // good
    const $sidebarBtn = $('.sidebar-btn');
    ```
2. 缓存 jQuery 查询。

    ```javascript
    // bad
    function setSidebar() {
      $('.sidebar').hide();
    
      // ...
    
      $('.sidebar').css({
        'background-color': 'pink',
      });
    }
    
    // good
    function setSidebar() {
      const $sidebar = $('.sidebar');
      $sidebar.hide();
    
      // ...
    
      $sidebar.css({
        'background-color': 'pink',
      });
    }
    ```
3. 对 DOM 查询使用层叠 `$('.sidebar ul')` 或 父元素 > 子元素 `$('.sidebar > ul')`。[jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)
4. 对有作用域的 jQuery 对象查询使用 `find`。

    ```javascript
    // bad
    $('ul', '.sidebar').hide();
    
    // bad
    $('.sidebar').find('ul').hide();
    
    // good
    $('.sidebar ul').hide();
    
    // good
    $('.sidebar > ul').hide();
    
    // good
    $sidebar.find('ul').hide();
    ```
## ECMAScript 5 兼容性
1. 参考[ Kangax](https://twitter.com/kangax/) 的 ES5 [兼容性表格](https://kangax.github.io/es5-compat-table/)。

## ECMAScript 6+（ES 2015+）规范
1. 以下是链接到 ES6 各个特性的列表。
    1. 箭头函数
    2. 类
    3. 对象方法缩写
    4. 对象属性缩写
    5. 对象可计算属性
    6. 模板字符串
    7. 解构
    8. 默认参数
    9. 剩余参数
    10. 数组展开
    11. Let 和 Const
    12. 指数操作符
    13. 迭代器和生成器
    14. 模块

2. 不要使用尚未达到stage 3 的[TC39 提议](https://github.com/tc39/proposals)。
    > 为什么？它们没有完全确定，可能会改变或者完全撤回。我们使用JavaScript，但是提议还不是JavaScript。

## 标准库
[标准库](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects)由于遗留原因包含了一些工具方法。
1. 使用 `Number.isNaN`而不是全局的`isNaN`。 eslint: [no-restricted-globals](https://eslint.org/docs/rules/no-restricted-globals)
    > 为什么？全局的`isNaN`方法转换非数字到数字类型，如果转换结果是NaN则返回true。如果这是你要的行为，请使用Number方法显示表明。

    ```javascript
    // bad
    isNaN('1.2'); // false
    isNaN('1.2.3'); // true
    
    // good
    Number.isNaN('1.2.3'); // false
    Number.isNaN(Number('1.2.3')); // true
    ```
2. 使用`Number.isFinite`而不是全局的`isFinite`方法。eslint: [no-restricted-globals](https://eslint.org/docs/rules/no-restricted-globals)
    > 为什么？全局的`isFinite`方法强制将非数字转成数字类型，如果转换结果是一个有限数字则返回true。如果这是你要的行为，请使用Number方法显示表明。

    ```javascript
    // bad
    isFinite('2e3'); // true
    
    // good
    Number.isFinite('2e3'); // false
    Number.isFinite(parseInt('2e3', 10)); // true
    ```

## 测试
1. **取巧**

    ```javascript
    function foo() {
      return true;
    }
    ```
2. **不！需要认真对待**
    - 无论使用哪种测试框架，都应该写测试用例。
    - 尽量写小的纯函数，并减少修改的部分。
    - 谨慎地使用假数据，这样会使你的测试代码变得脆弱。
    - [在Airbnb我们主要使用mocha](https://www.npmjs.com/package/mocha)。[tape](https://www.npmjs.com/package/tape)也偶尔用于小型独立模块的测试。
    - 100％的测试覆盖率是一个很好的追求目标，即使它并不总是实际可行的。
    - 每当你修复一个bug，写一个回归测试。 未经回归测试修正的bug几乎在未来肯定会再次出问题。
