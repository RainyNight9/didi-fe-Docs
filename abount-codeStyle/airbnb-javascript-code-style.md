# Airbnb JavaScript 编码风格指南
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

    ```
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

    ```
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

    ```
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
    
    ```
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

    ```
    // bad
    const foo = '\'this\' \i\s \"quoted\"';
    
    // good
    const foo = '\'this\' is "quoted"';
    const foo = `my name is '${name}'`;
    ```