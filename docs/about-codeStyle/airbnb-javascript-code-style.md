# Airbnb JavaScript 编码风格指南
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
