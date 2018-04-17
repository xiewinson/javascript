# JavaScript 规范指南
> 翻译自 [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript#variables)
## 类型（Types）
* 基本类型: 直接使用它的值就行
    * `string`
    * `number`
    * `boolean`
    * `null`
    * `undefined`
    * `symbol`

	  ```JavaScript
    const foo = 1;
    let bar = foo;
    bar = 9;
    console.log(foo, bar); // => 1, 9
    ```
* 复杂类型: 使用引用去访问
    * `object`
    * `array`
    * `function`

	  ```JavaScript
    const foo = [1, 2];
    const bar = foo;
    bar[0] = 9;
    console.log(foo[0], bar[0]); // => 9, 9
    ```
## 引用（References）
* 使用 `const` 声明引用类型，避免使用 `var`
    > 为啥？这会确保你不能重新给引用赋值，以免造成 bug，也避免难以理解这段代码

	```JavaScript
    // 别这样
    var a = 1;
    var b = 2;

    // 要这样
    const a = 1;
    const b = 2;
    ```
* 如果偏要重新赋值，使用 `let` 代替 `var`

	> 为啥？`let` 是块级作用域，`var` 作为函数级作用域会发生变量提升，在很多场景下没有必要并且难以理解那段代码

	```JavaScript
    // 别这样
    var count = 1;
    if (true) {
        count += 1;
    }

    // 要这样，用 let
    let count = 1;
    if (true) {
        count += 1;
    }
    ```
* 请注意 `let` 和 `const` 是块级作用域

	```JavaScript
    // const 和 let 只会存在于所定义的块中
    {
        let a = 1;
        const b = 1;
    }
    console.log(a); // 报错ReferenceError
    console.log(b); // 报错ReferenceError
    ```
## 对象（Objects）
* 创建对象使用字面量语法，简洁明了

	```JavaScript
    // 别这样
    const item = new Object();

    // 要这样
    const item = {};
    ```
* 如果使用了动态属性名创建对象，那么请使用计算属性名
    > 为啥？这可以在一个地方去声明对象里所有的属性

	```JavaScript
    function getKey(k) {
        return `a key named ${k}`;
    }

    // 别这样
    const obj = {
        id: 5,
        name: 'San Francisco',
    };
    obj[getKey('enabled')] = true;

    // 要这样
    const obj = {
        id: 5,
        name: 'San Francisco',
        [getKey('enabled')]: true,
    };
    * 简写对象中的方法

	```JavaScript
    // 别这样
    const atom = {
        value: 1,

        addValue: function (value) {
        return atom.value + value;
        },
    };

    // 要这样
    const atom = {
        value: 1,

        addValue(value) {
        return atom.value + value;
        },
    };
    ```
* 简写对象中的属性值
    > 为啥？方便写，更易读

	```JavaScript
    const lukeSkywalker = 'Luke Skywalker';

    // 别这样
    const obj = {
        lukeSkywalker: lukeSkywalker,
    };

    // 要这样
    const obj = {
        lukeSkywalker,
    };

    ```
* 声明对象时，简写的属性写在前面
    > 为啥？更加容易理解哪个属性用了些啥简写

	```JavaScript
    const anakinSkywalker = 'Anakin Skywalker';
    const lukeSkywalker = 'Luke Skywalker';

    // 别这样，乱！
    const obj = {
        episodeOne: 1,
        twoJediWalkIntoACantina: 2,
        lukeSkywalker,
        episodeThree: 3,
        mayTheFourth: 4,
        anakinSkywalker,
    };

    // 要这样
    const obj = {
        lukeSkywalker,
        anakinSkywalker,
        episodeOne: 1,
        twoJediWalkIntoACantina: 2,
        episodeThree: 3,
        mayTheFourth: 4,
    };
    ```
* 只在不符合 JS 取名规则的属性名上加引号
    > 为啥？可读性更好，还可以语法高亮，并且更加容易被 JS 引擎优化

	```JavaScript
    // 只在无效属性名
    const bad = {
        'foo': 3,
        'bar': 4,
        'data-blah': 5,
    };

    // 要这样
    const good = {
        foo: 3,
        bar: 4,
        'data-blah': 5,
    };
    ```
* 别直接使用 `Object.prototype` 中的方法，比如 `hasOwnProperty`，`propertyIsEnumerable`, `isPrototypeOf` 等等
    > 为啥？这些方法可能被对象中的属性覆盖，对象也可能是空的，比如 `Object.create(null)` 创建的对象

	```JavaScript
    // 别这样
    console.log(object.hasOwnProperty(key));

    // 要这样
    console.log(Object.prototype.hasOwnProperty.call(object, key));

    // 这样最佳，在 module 级缓存
    const has = Object.prototype.hasOwnProperty;

    /* or */
    import has from 'has';
    console.log(has.call(object, key));
    ```
    * 浅拷贝对象时，使用扩展（spread）操作符比 `Object.assign` 更好，可使用剩余（rest）操作符去获得一个新的包含被省略的属性的对象

	```JavaScript
    // 特糟糕
    const original = { a: 1, b: 2 };
    // 下面这个骚操作改变了 original 对象原有的属性 ಠ_ಠ
    const copy = Object.assign(original, { c: 3 });
    delete copy.a;

    // 也别这样
    const original = { a: 1, b: 2 };
    const copy = Object.assign({}, original, { c: 3 });
    // copy => { a: 1, b: 2, c: 3 }

    // 要这样
    const original = { a: 1, b: 2 };
    const copy = { ...original, c: 3 };
    // copy => { a: 1, b: 2, c: 3 }

    const { a, ...noA } = copy;
    // noA => { b: 2, c: 3 }
    ```
## 数组（Arrays）
* 创建数组时使用字面量语法

	```JavaScript
    // 别这样
    const items = new Array();

    // 要这样
    const items = [];
    ```
* 向数组添加元素时使用 `Array#push` 替代直接指定

	```JavaScript
    const someStack = [];

    // 别这样
    someStack[someStack.length] = 'abracadabra';

    // 要这样
    someStack.push('abracadabra');
    ```
* 使用扩展（spread）操作符 `...` 拷贝数组

	```JavaScript
    // 别这样
    const len = items.length;
    const itemsCopy = [];
    let i;
    for (i = 0; i < len; i += 1) {
        itemsCopy[i] = items[i];
    }

    // 要这样
    const itemsCopy = [...items];
    ```
* 将类数组对象（array-like object）转换为数组时用扩展（spread）操作符 `...` 代替 `Array.from`

	```JavaScript
    const foo = document.querySelectorAll('.foo');

    // 也还行
    const nodes = Array.from(foo);

    // 要这样
    const nodes = [...foo];
    ```
* 当对可迭代对象执行 map 操作时使用 `Array.from` 代替扩展操作符 `...`，因为这将避免创建一个临时的中间数组

	```JavaScript
    // 别这样
    const baz = [...foo].map(bar);

    // 要这样
    const baz = Array.from(foo, bar);
    ```
* 在数组方法的回调中要使用 return 语句，并且考虑使用箭头函数

	```JavaScript
    // 要这样
    [1, 2, 3].map((x) => {
        const y = x + 1;
        return x * y;
    });

    // 要这样
    [1, 2, 3].map(x => x + 1);

    // 别这样，没有返回意味着第一轮迭代后 acc 变成 undefined
    [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
        const flatten = acc.concat(item);
        acc[index] = flatten;
    });

    // 要这样
    [[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
        const flatten = acc.concat(item);
        acc[index] = flatten;
        return flatten;
    });

    // 别这样
    inbox.filter((msg) => {
        const { subject, author } = msg;
        if (subject === 'Mockingbird') {
            return author === 'Harper Lee';
        } else {
            return false;
        }
    });

    // 要这样
    inbox.filter((msg) => {
        const { subject, author } = msg;
        if (subject === 'Mockingbird') {
            return author === 'Harper Lee';
        }

        return false;
    });
    ```
* 当数组有多行时，在输入数组左半边括号之后和输入数组右半边括号之前使用换行符

	```JavaScript
    // 别这样
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

    // 要这样
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
## 解构（Destructuring）
* 当需要访问和使用对象的多个属性时使用对象解构
    > 为啥？解构让你避免了为那几个属性创建临时的引用

	```JavaScript
    // 别这样
    function getFullName(user) {
        const firstName = user.firstName;
        const lastName = user.lastName;

        return `${firstName} ${lastName}`;
    }

    // 要这样
    function getFullName(user) {
        const { firstName, lastName } = user;
        return `${firstName} ${lastName}`;
    }

    // 最佳
    function getFullName({ firstName, lastName }) {
        return `${firstName} ${lastName}`;
    }
    ```
* 使用数组解构

	```JavaScript
    const arr = [1, 2, 3, 4];

    // 别这样
    const first = arr[0];
    const second = arr[1];

    // 要这样
    const [first, second] = arr;
    ```
* 多个值返回时使用对象解构，别用数组解构
    > 为啥？这样做你后面可以更方便地添加其他属性或者改变顺序

	```JavaScript
    // 别这样
    function processInput(input) {
        // then a miracle occurs
        return [left, right, top, bottom];
    }

    // 调用者需要去考虑返回数据的顺序
    const [left, **, top] = processInput(input);

    // 要这样
    function processInput(input) {
        // then a miracle occurs
        return { left, right, top, bottom };
    }

    // 调用者只用宣传他们需要的部分
    const { left, top } = processInput(input);
    ```
## 字符串（Strings）
* 使用单引号`''`

	```JavaScript
    // 别这样
    const name = "Capt. Janeway";

    // 别这样 - 包含插值或换行时才使用模板语法
    const name = `Capt. Janeway`;

    // 要这样
    const name = 'Capt. Janeway';
    ```
* 没超过100个字符的字符串不要进行换行
    > 为啥？换行是个痛苦的事，并且不利于代码搜索

	```JavaScript
    // 别这样
    const errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';

    // 别这样
    const errorMessage = 'This is a super long error that was thrown because ' +
    'of Batman. When you stop to think about how Batman had anything to do ' +
    'with this, you would get nowhere fast.';

    // 要这样
    const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';
    ```
* 要在代码中构建字符串时，使用模板字符串替代字符串连接符
    > 为啥？使用模板字符串更加易读，并且拥有简洁的换行和插值语法

	```JavaScript
    // 别这样
    function sayHi(name) {
        return 'How are you, ' + name + '?';
    }

    // 也别这样
    function sayHi(name) {
        return ['How are you, ', name, '?'].join();
    }

    // 别这样
    function sayHi(name) {
        return `How are you, ${ name }?`;
    }

    // 要这样
    function sayHi(name) {
        return `How are you, ${name}?`;
    }
    ```
* 不要使用 `eval()` 方法，这会导致很多问题
* 去掉不必要的转义
    > 为啥？反斜杠让代码可读性太差，因此我们只在必须的时候才用

	```JavaScript
    // 别这样
    const foo = '\'this\' \i\s \"quoted\"';

    // 要这样
    const foo = '\'this\' is "quoted"';
    const foo = `my name is '${name}'`;
    ```
## 函数（Functions）
* 使用命名函数表达式替代函数声明
    > 为啥？函数声明会被提升，这意味可以轻易地在函数定义之前去引用函数，这样可读性和可维护性太差。如果函数的定义太长或者非常复杂，这很干扰对文件剩余部分的理解，那就到了可以把它提取到自己专属模块的时候了！不要忘记给函数取一个贴切的名字，尽管函数名字可能可以从包含的变量中推断出来（现代浏览器或者 Babel 等编译器可以做到）。这让错误的调用栈消除臆测

	```JavaScript
    // 别这样
    function foo() {
    // ...
    }

    // 别这样
    const foo = function () {
    // ...
    };

    // 要这样
    // 函数名不同于变量引用调用名称
    const short = function longUniqueMoreDescriptiveLexicalFoo() {
    // ...
    };
    ```
* 用小括号报过立即调用函数表达式（IIFE）
    > 为啥？一个立即调用函数表达式是一个独立的单元，所以要包裹起来，这样也表达得更加清晰。注意在通用 module 中的任何地方都不应该使用 IIFE

	```JavaScript
    // immediately-invoked function expression (IIFE)
    (function () {
        console.log('Welcome to the Internet. Please follow me.');
    }());
    ```
* 不要在定义非函数代码块（if, while, 等等）声明方法。指派函数给一个变量来替代。浏览器将允许你这样做，但他们解析这语句可能有所不同。
* 注意: ECMA-262 将 `block` 定义为一组语句，函数的声明不是一个语句

	```JavaScript
    // 别这样
    if (currentUser) {
        function test() {
            console.log('Nope.');
        }
    }

    // 要这样
    let test;
    if (currentUser) {
        test = () => {
            console.log('Yup.');
        };
    }
    ```
* 不要把一个参数命名为 `arguments`，这将排挤掉函数作用域中的 `arguments` 对象
    ``` JavaScript
    // 别这样
    function foo(name, options, arguments) {
        // ...
    }

    // 要这样
    function foo(name, options, args) {
        // ...
    }
    ```
* 使用剩余（rest）操作符 `...` 替代 `arguments`
    > 为啥？`...` 能更明确哪些参数是你想要拉取的。更重要的是，这是一个真正的数组，而不是 `arguments` 这样的仅仅是类数组对象
    ``` JavaScript
    // 别这样
    function concatenateAll() {
        const args = Array.prototype.slice.call(arguments);
        return args.join('');
    }

    // 要这样
    function concatenateAll(...args) {
        return args.join('');
    }
    ```
* 使用默认参数表达式比使用可改变的函数参数更好

	```JavaScript
    // 特别惨
    function handleThings(opts) {
        // 不! 我们不能改变函数的参数
        // 双重糟糕: 如果 opts 是一个布尔值这将设置为一个对象，可能导致一些 bug
        opts = opts || {};
        // ...
    }

    // 依旧惨
    function handleThings(opts) {
        if (opts === void 0) {
            opts = {};
        }
    // ...
    }

    // 要这样
    function handleThings(opts = {}) {
        // ...
    }
    ```
* 避免默认参数的副作用
    > 为啥？太混淆了

	```JavaScript
    var b = 1;
    // 别这样
    function count(a = b++) {
    console.log(a);
    }
    count();  // 1
    count();  // 2
    count(3); // 3
    count();  // 3
    ```
* 把默认参数放在最后一个

	```JavaScript
    // 别这样
    function handleThings(opts = {}, name) {
        // ...
    }

    // 要这样
    function handleThings(name, opts = {}) {
        // ...
    }
    ```
* 不要使用函数构造器创建新的函数
    > 为啥？这种通过字符串创建函数的方式类似于 `eval()`，这可能导致一些问题

	```JavaScript
    // 别这样
    var add = new Function('a', 'b', 'return a + b');

    // 依然别这样
    var subtract = Function('a', 'b', 'return a - b');
    ```
* 在方法签名处打空格
    > 为啥？一致性是好的，你不应该增加或移除空格当增加或移除一个名字
    ``` JavaScript
    // 别这样
    const f = function(){};
    const g = function (){};
    const h = function() {};

    // 要这样
    const x = function () {};
    const y = function a() {};
    ```
* 不要重新赋值函数参数
    > 为啥？重新赋值参数会导致不可预计的行为，特别是访问 `arguments` 对象。这也会引起性能问题，特别是在 V8 中

	```JavaScript
    // 别这样
    function f1(a) {
        a = 1;
        // ...
    }

    function f2(a) {
    if (!a) { a = 1; }
        // ...
    }

    // 要这样
    function f3(a) {
        const b = a || 1;
        // ...
    }

    function f4(a = 1) {
    // ...
    }
    ```
* 对于可变参数的函数推荐使用扩展操作符 `...`
    > 为啥？清晰！你不需要提供一个 context，组合 `new` 和 `apply` 也不太易用
    ``` JavaScript
    // 别这样
    const x = [1, 2, 3, 4, 5];
    console.log.apply(console, x);

    // 要这样
    const x = [1, 2, 3, 4, 5];
    console.log(...x);

    // 别这样
    new (Function.prototype.bind.apply(Date, [null, 2016, 8, 5]));

    // 要这样
    new Date(...[2016, 8, 5]);
    ```
* 有多个签名的函数或者调用，应该让每一个都换行缩进，左对齐并且结尾跟一个逗号（包括最后一个参数）

	```JavaScript
     // 别这样
    function foo(bar,
                baz,
                quux) {
        // ...
    }

    // 要这样
    function foo(
        bar,
        baz,
        quux,
    ) {
        // ...
    }

    // 别这样
    console.log(foo,
        bar,
        baz);

    // 要这样
    console.log(
        foo,
        bar,
        baz,
    );
    ```
## 箭头函数（Arrow Functions）
* 当你必须使用匿名函数时（比如当传递一个内联回调），使用箭头函数
    > 为啥？箭头函数中的 `this` 指代的是定义函数时所处的对象，而非执行时的对象，在符合通常的使用情景，而且这个语法更简洁

    >为啥不呢？如果是一个相当复杂的方法，你可能应该把逻辑转移到另一个函数表达式上

	```JavaScript
    // 别这样
    [1, 2, 3].map(function (x) {
        const y = x + 1;
        return x * y;
    });

    // 要这样
    [1, 2, 3].map((x) => {
        const y = x + 1;
        return x * y;
    });
    ```
* 如果方法体的构成是一条单一的且无副作用的返回语句，省略花符号并使用隐式返回，否则，保持连接符号和 `return` 语句
    > 为啥？语法糖。链式调用时可读性不错

	```JavaScript
    // 别这样
    [1, 2, 3].map(number => {
        const nextNumber = number + 1;
        `A string containing the ${nextNumber}.`;
    });

    // 要这样
    [1, 2, 3].map(number => `A string containing the ${number}.`);

    // 要这样
    [1, 2, 3].map((number) => {
        const nextNumber = number + 1;
        return `A string containing the ${nextNumber}.`;
    });

    // 要这样
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

    // 别这样
    foo(() => bool = true);

    // 要这样
    foo(() => {
        bool = true;
    });
    ```
* 如果表达式有多行，用括号包裹起来可读性更好
    > 为啥？折让函数的开始和结尾都会清晰
    ``` JavaScript
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
* 如果你的函数只有单行且没有用花符号，那就省略括号，否则就保持括号包裹参数以达到清晰与一致性
    > 为啥？视觉一致性

	```JavaScript
    // 别这样
    [1, 2, 3].map((x) => x * x);

    // 要这样
    [1, 2, 3].map(x => x * x);

    // 要这样
    [1, 2, 3].map(number => (
        `A long string with the ${number}. It’s so long that we don’t want it to take up space on the .map line!`
    ));

    // 别这样
    [1, 2, 3].map(x => {
        const y = x + 1;
        return x * y;
    });

    // 要这样
    [1, 2, 3].map((x) => {
        const y = x + 1;
        return x * y;
    });
    ```
* 箭头函数表达式（`=>`）与比较操作符（`<=`, `>=`）混用时保持代码的清晰

	```JavaScript
    // 别这样
    const itemHeight = item => item.height > 256 ? item.largeSize : item.smallSize;

    // 别这样
    const itemHeight = (item) => item.height > 256 ? item.largeSize : item.smallSize;

    // 要这样
    const itemHeight = item => (item.height > 256 ? item.largeSize : item.smallSize);

    // 要这样
    const itemHeight = (item) => {
        const { height, largeSize, smallSize } = item;
        return height > 256 ? largeSize : smallSize;
    };
    ```
## 类和构造器（Classes & Constructors）
* 使用 `class`，避免直接使用 `prototype`
    > 为啥？`class` 关键词更加简洁和易读

	```JavaScript
    // 别这样
    function Queue(contents = []) {
        this.queue = [...contents];
    }
    Queue.prototype.pop = function () {
        const value = this.queue[0];
        this.queue.splice(0, 1);
        return value;
    };

    // 要这样
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
* 继承时使用 `extends`
    > 为啥？这是一个内建方式继承，且不会破坏 `instanceof`

	```JavaScript
    // 别这样
    const inherits = require('inherits');
    function PeekableQueue(contents) {
        Queue.apply(this, contents);
    }
    inherits(PeekableQueue, Queue);
    PeekableQueue.prototype.peek = function () {
        return this.queue[0];
    };

    // 要这样
    class PeekableQueue extends Queue {
        peek() {
            return this.queue[0];
        }
    }
    ```
* 为实现链式编程，方法应该返回 `this`

	```JavaScript
    // 别这样
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

    // 要这样
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
* 可以自定义 `toString()` 方法，只要保证运行没问题且无副作用就成

	```JavaScript
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
* 不指定时，类有一个默认的构造器，所以没必要去写一个空构造器或者只是委派父类的构造器

	```JavaScript
    // 别这样
    class Jedi {
        constructor() {}

        getName() {
            return this.name;
        }
    }

    // 别这样
    class Rey extends Jedi {
        constructor(...args) {
            super(...args);
        }
    }

    // 要这样
    class Rey extends Jedi {
        constructor(...args) {
            super(...args);
            this.name = 'Rey';
        }
    }
    ```
* 避免重复声明类成员
    > 为啥？重复声明的情况只有最后一个生效，毫无疑问重复声明是个 bug

	```JavaScript
    // 别这样
    class Foo {
        bar() { return 1; }
        bar() { return 2; }
    }

    // 要这样
    class Foo {
        bar() { return 1; }
    }

    // 要这样
    class Foo {
        bar() { return 2; }
    }
    ```
## 模块
* 使用 `import/export`，不使用非标准的模块系统。你可以编译成你偏爱的模块系统
    > 为啥？模块是未来，让我们现在开始享受未来

	```JavaScript

    // 别这样
    const AirbnbStyleGuide = require('./AirbnbStyleGuide');
    module.exports = AirbnbStyleGuide.es6;

    // 不错
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    export default AirbnbStyleGuide.es6;

    // 最佳
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```
* 不要使用通配符导入
    > 为啥？确保你只有一个单一的默认导出

	```JavaScript
    // 别这样
    import * as AirbnbStyleGuide from './AirbnbStyleGuide';

    // 要这样
    import AirbnbStyleGuide from './AirbnbStyleGuide';
    ```
* 不要直接从 `import` 中 `export`
    > 为啥？尽管一条语句很简洁，不过清晰地分开导入和导出更好

	```JavaScript
    // 别这样
    // filename es6.js
    export { es6 as default } from './AirbnbStyleGuide';

    // 要这样
    // filename es6.js
    import { es6 } from './AirbnbStyleGuide';
    export default es6;
    ```
* 一个路径只使用一次 `import`
    > 为啥？对相同的路径有多行的 `import` 会让代码难以维护

	```JavaScript
    // 别这样
    import foo from 'foo';
    // … some other imports … //
    import { named1, named2 } from 'foo';

    // 要这样
    import foo, { named1, named2 } from 'foo';

    // 要这样
    import foo, {
    named1,
    named2,
    } from 'foo';
    ```
* 不要导出可变绑定
    > 为啥？避免导出可变量。这个只应应用于特殊的场景，一般情况下只有不变的引用才应该导出

	```JavaScript
    // 别这样
    let foo = 3;
    export { foo };

    // 要这样
    const foo = 3;
    export { foo };
    ```
* 如果模块中只需一个单一的导出，使用默认导出的方式更好
    > 为啥？为了鼓励更多文件只导出一个东西，这会有更好的可读性和可维护性

	```JavaScript
    // 别这样
    export function foo() {}

    // 要这样
    export default function foo() {}
    ```
* 所有的 `import` 语句放在非导入语句的上方
    > 为啥？保持它们在顶部防止出现奇怪的事情

	```JavaScript
    // bad
    import foo from 'foo';
    foo.init();

    import bar from 'bar';

    // good
    import foo from 'foo';
    import bar from 'bar';

    foo.init();
    ```
* 多行的导入应该缩进并换行，像多行的数组和对象那种一样
    > 为啥？应该遵循同规范中其他处一样的样式

	```JavaScript
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
* 禁止在模块导入语句中使用 Webpack 的加载器语法
    > 为啥？这样做会导致代码耦合，更好的做法是在 `webpack.config.js` 中使用加载器语法。

	```JavaScript
    // 别这样
    import fooSass from 'css!sass!foo.scss';
    import barCss from 'style!css!bar.css';

    // 要这样
    import fooSass from 'foo.scss';
    import barCss from 'bar.css';
    ```
## 迭代器和生成器（Iterators and Generators）
* 不要使用迭代，使用 JavaScript 的高阶函数代替例如 `for-in` 或者 `for-of`
    > 为啥？相比副作用，处理单纯函数的返回值更易理解

    > 使用 `map()` / `every()` / `filter()` / `find()` / `findIndex()` / `reduce()` / `some()` / ... 等等来遍历数组, 使用 `Object.keys()` / `Object.values()` / `Object.entries()`来生成数组就可以遍历对象了

	```JavaScript
    const numbers = [1, 2, 3, 4, 5];

    // 别这样
    let sum = 0;
    for (let num of numbers) {
        sum += num;
    }
    sum === 15;

    // 要这样
    let sum = 0;
    numbers.forEach((num) => {
        sum += num;
    });
    sum === 15;

    // 最佳
    const sum = numbers.reduce((total, num) => total + num, 0);
    sum === 15;

    // 别这样
    const increasedByOne = [];
    for (let i = 0; i < numbers.length; i++) {
        increasedByOne.push(numbers[i] + 1);
    }

    // 要这样
    const increasedByOne = [];
    numbers.forEach((num) => {
        increasedByOne.push(num + 1);
    });

    // 最佳
    const increasedByOne = numbers.map(num => num + 1);
    ```
* 目前不要使用生成器
    > 为啥？这还不能很好的编译到 ES5
* 如果你必须使用生成器，或者你无视我们的忠告，请确保函数签名有正确的间隔
    > 为啥？函数和 `*` 有些概念上的相似，`*` 不是函数的修饰符，`function*` 是一个独特的构造器，不同于 `function`

	```JavaScript
    // 别这样
    function * foo() {
        // ...
    }

    // 别这样
    const bar = function * () {
        // ...
    };

    // 别这样
    const baz = function *() {
        // ...
    };

    // 别这样
    const quux = function*() {
        // ...
    };

    // 别这样
    function*foo() {
        // ...
    }

    // 别这样
    function *foo() {
        // ...
    }

    // 特糟糕
    function
    *
    foo() {
        // ...
    }

    // 特糟糕
    const wat = function
    *
    () {
        // ...
    };

    // 要这样
    function* foo() {
        // ...
    }

    // 要这样
    const foo = function* () {
        // ...
    };
    ```
## 属性（Properties）
* 访问对象属性时使用英文句点

	```JavaScript
    const luke = {
        jedi: true,
        age: 28,
    };

    // 别这样
    const isJedi = luke['jedi'];

    // 要这样
    const isJedi = luke.jedi;
    ```
* 当使用变量访问时使用 `[]`

	```JavaScript
    const luke = {
        jedi: true,
        age: 28,
    };

    function getProp(prop) {
        return luke[prop];
    }

    const isJedi = getProp('jedi');
    ```
* 计算乘方时使用幂运算符 `**`

	```JavaScript
    // 别这样
    const binary = Math.pow(2, 10);

    // 要这样
    const binary = 2 ** 10;
    ```
## 变量（Variables）
* 使用 `const` 或者 `let` 声明变量，否则会是全局变量。我们要避免污染全局变量，行星队长警告过我们

	```JavaScript
    // 别这样
    superPower = new SuperPower();

    // 要这样
    const superPower = new SuperPower();
    ```
* 每个变量用单独的 `const` 或 `let` 声明
    > 为啥？这样做就很轻松去增加新的变量，你不用担心去写 `;` 还是 `,`。此外，在调试的时候你还可以进行单步调试而不是一次全部跳过

	```JavaScript
    // 别这样
    const items = getItems(),
        goSportsTeam = true,
        dragonball = 'z';

    // 别这样
    // (compare to above, and try to spot the mistake)
    const items = getItems(),
        goSportsTeam = true;
        dragonball = 'z';

    // 要这样
    const items = getItems();
    const goSportsTeam = true;
    const dragonball = 'z';
    ```
* 把 `const` 变量和 `let` 变量分组声明
    > 为啥？当你需要声明一个依赖于前面声明的变量的变量时你会发现这样分组很有用

	```JavaScript
    // 别这样
    let i, len, dragonball,
        items = getItems(),
        goSportsTeam = true;

    // 别这样
    let i;
    const items = getItems();
    let dragonball;
    const goSportsTeam = true;
    let len;

    // 要这样
    const goSportsTeam = true;
    const items = getItems();
    let dragonball;
    let i;
    let length;
    ```
* 在需要的地方需要的时候再给变量赋值
    > 为啥？`let` 和 `const` 是块级作用域有效，不是函数级作用域

	```JavaScript
    // 别这样
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

    // 要这样
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
* 不要链条形式给变量赋值
    > 为啥？链式赋值会创建隐式全局变量

	```JavaScript
    // 别这样
    (function example() {

        // let 只作用域 a，b、c 会变成全局变量
        let a = b = c = 1;
    }());

    console.log(a); // throws ReferenceError
    console.log(b); // 1
    console.log(c); // 1

    // 要这样
    (function example() {
        let a = 1;
        let b = a;
        let c = a;
    }());

    console.log(a); // throws ReferenceError
    console.log(b); // throws ReferenceError
    console.log(c); // throws ReferenceError

    // the same applies for `const`
    ```
* 不要使用 `++` 和 `--`
    > 为啥？根据 eslint 文档，一元递增和递减语句会受到自动分号插入的影响，并且可能导致应用程序中的值递增或递减，从而导致无提示错误。使用像 num += 1 而不是 num++ 或 num++ 这样的语句来更改数值也更具有表现力。禁止一元递增和递减语句也会阻止您无意中预先递增/预递减值，这也会导致程序中的意外行为

	```JavaScript
    // 别这样
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

    // 要这样
    const array = [1, 2, 3];
    let num = 1;
    num += 1;
    num -= 1;

    const sum = array.reduce((a, b) => a + b, 0);
    const truthyCount = array.filter(Boolean).length;
    ```
* 给变量赋值时避免在 `=` 后面换行。如果一定要这样做，用 `()` 包裹起来
    > 为啥？这种换号让赋值看起来很混淆

	```JavaScript
    // 别这样
    const foo =
    superLongLongLongLongLongLongLongLongFunctionName();

    // 别这样
    const foo
    = 'superLongLongLongLongLongLongLongLongString';

    // 要这样
    const foo = (
    superLongLongLongLongLongLongLongLongFunctionName()
    );

    // 要这样
    const foo = 'superLongLongLongLongLongLongLongLongString';
    ```
## 提升（Hoisting）
* 使用 `var` 关键字声明变量会使它的作用范围提升到它所在的函数作用域，但赋值不会。`let` 和 `const` 不会有这样的表现，但是在声明 `let` 或 `const` 变量之前对这个变量使用 `typeof` 会抛出 `ReferenceError`

	```JavaScript
    // 这没法工作（如果没有定义一个叫 notDefined 的全局变量）
    function example() {
        console.log(notDefined); // => throws a ReferenceError
    }

    // 能成功运行，声明提升，但赋值没有提升
    function example() {
        console.log(declaredButNotAssigned); // => undefined
        var declaredButNotAssigned = true;
    }

    // 也能成功运行
    function example() {
        let declaredButNotAssigned;
        console.log(declaredButNotAssigned); // => undefined
        declaredButNotAssigned = true;
    }

    // 使用 const 和 let 会报错
    function example() {
        console.log(declaredButNotAssigned); // => throws a ReferenceError
        console.log(typeof declaredButNotAssigned); // => throws a ReferenceError
        const declaredButNotAssigned = true;
    }
    ```
* 匿名函数表达式也会发生变量提升，但是函数的赋值内容不会

	```JavaScript
    function example() {
        console.log(anonymous); // => undefined

        anonymous(); // => TypeError anonymous is not a function

        var anonymous = function () {
            console.log('anonymous function expression');
        };
    }
    ```
* 命名函数表达式会发生变量提升，但是方法名和方法体都不会

	```JavaScript
    function example() {
        console.log(named); // => undefined

        named(); // => TypeError named is not a function

        superPower(); // => ReferenceError superPower is not defined

        var named = function superPower() {
            console.log('Flying');
        };
    }

    // 当函数变量和函数名称一样时也是如此
    function example() {
        console.log(named); // => undefined

        named(); // => TypeError named is not a function

        var named = function named() {
            console.log('named');
        };
    }
    ```
* 函数声明方式会提升名称和函数体

	```JavaScript
    function example() {
        superPower(); // => Flying

        function superPower() {
            console.log('Flying');
        }
    }
    ```
## 比较运算符和相等（Comparison Operators & Equality）
* 使用 `===` 和 `!==` 代替 `==` 和 `!=`
* 条件语句比如 `if` 使用 abstract 方法 `ToBoolean` 强制使其遵循以下规则:
    * **Objects** 被计算成 **true**
    * **Undefined** 被计算成 **false**
    * **Null** 被计算成 **false**
    * **Booleans** 被计算为 **boolean的值**
    * **Numbers** 在值为 **+0, -0, NaN 时为false**，其他情况为 **true**
    * **Strings** 在 **空字符串`''`** 时为 **false**，其他情况为 **true**

	```JavaScript
    if ([0] && []) {
        // true
        // 一个数组（即便是空数组）是一个对象，对象就会被计算为 true
    }
    ```
* 比较布尔类型时用简写形式，但显式比较字符串和数字

	```JavaScript
    // 别这样
    if (isValid === true) {
        // ...
    }

    // 要这样
    if (isValid) {
        // ...
    }

    // 别这样
    if (name) {
        // ...
    }

    // 要这样
    if (name !== '') {
        // ...
    }

    // 别这样
    if (collection.length) {
        // ...
    }

    // 要这样
    if (collection.length > 0) {
        // ...
    }
    ```
* 在 `case`、`default` 中，使用花括号创建代码块来包裹含有 `let`、`const`、`function`、`class` 等声明语法的语句
    > 为啥？声明语法是在整个 `swtich` 中是可见了，但只要赋值了才会初始化，这只会在走到它的 `case` 语句时才会发生。在多个 `case` 语句中声明相同的东西时会出现问题

	```JavaScript
    // 别这样
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

    // 要这样
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
* 三元操作符不应该嵌套使用，并且通常应该放在单行

	```JavaScript
    // 别这样
    const foo = maybe1 > maybe2
        ? "bar"
        : value1 > value2 ? "baz" : null;

    // 分成两个三元操作符
    const maybeNull = value1 > value2 ? 'baz' : null;

    // 更好
    const foo = maybe1 > maybe2
        ? 'bar'
        : maybeNull;

    // 最佳
    const foo = maybe1 > maybe2 ? 'bar' : maybeNull;
    ```
* 避免无必要的三元操作符

	```JavaScript
    // 别这样
    const foo = a ? a : b;
    const bar = c ? true : false;
    const baz = c ? false : true;

    // 要这样
    const foo = a || b;
    const bar = !!c;
    const baz = !c;
    ```
* 混合使用操作符时，用小括号包裹起来。唯一的例外是标准算术运算符（`+`, `-`, `*`, `&`, `/`），因为它的存在优先级问题
    > 为啥？提高可读性，表达开发者的意图

	```JavaScript
    // 别这样
    const foo = a && b < 0 || c > 0 || d + 1 === 0;

    // 别这样
    const bar = a ** b - 5 % d;

    // 别这样
    // 可能会被人认为是 (a || b) && c
    if (a || b && c) {
        return d;
    }

    // 要这样
    const foo = (a && b < 0) || c > 0 || (d + 1 === 0);

    // 要这样
    const bar = (a ** b) - (5 % d);

    // 要这样
    if (a || (b && c)) {
        return d;
    }

    // 要这样
    const bar = a + b / c * d;
    ```
## 块（Blocks）
* 在多行块中使用花括号

	```JavaScript
    // 别这样
    if (test)
        return false;

    // 别这样
    if (test) return false;

    // 要这样
    if (test) {
        return false;
    }

    // 别这样
    function foo() { return false; }

    // 要这样
    function bar() {
        return false;
    }
    ```
* 如果你在 `if`、`else` 中使用了多行代码块，把 `else` 放在 `if` 块结束的花括号的同一行

	```JavaScript
    // 别这样
    if (test) {
        thing1();
        thing2();
    }
    else {
        thing3();
    }

    // 要这样
    if (test) {
        thing1();
        thing2();
    } else {
        thing3();
    }
    ```
* 如果一个 `if` 代码块总会执行 `return` 语句，那么之后的 `else` 代码块就不是必须的。如果包含返回值的 `if` 代码块之后的 `else if` 代码块也有 `return` 语句，那么可以分成多个 `if` 代码块

	```JavaScript
    // 别这样
    function foo() {
        if (x) {
            return x;
        } else {
            return y;
        }
    }

    // 别这样
    function cats() {
        if (x) {
            return x;
        } else if (y) {
            return y;
        }
    }

    // 别这样
    function dogs() {
        if (x) {
            return x;
        } else {
            if (y) {
            return y;
            }
        }
    }

    // 要这样
    function foo() {
        if (x) {
            return x;
        }

        return y;
    }

    // 要这样
    function cats() {
        if (x) {
            return x;
        }

        if (y) {
            return y;
        }
    }

    // 要这样
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
## 控制语句（Control Statement）
* 如果您的控制语句（`if`, `while` 等等）变得太长或超过最大一行长度，则可以将每个（分组）条件放入一个新行中。逻辑运算符应该放在开头
    > 为啥？要求操作符在行开头可以保持操作符对齐并遵循类似于方法链的规范。这也通过视觉上遵循复杂逻辑来提高可读性

	```JavaScript
    // 别这样
    if ((foo === 123 || bar === 'abc') && doesItLookGoodWhenItBecomesThatLong() && isThisReallyHappening()) {
        thing1();
    }

    // 别这样
    if (foo === 123 &&
        bar === 'abc') {
        thing1();
    }

    // 别这样
    if (foo === 123
        && bar === 'abc') {
        thing1();
    }

    // 别这样
    if (
        foo === 123 &&
        bar === 'abc'
    ) {
        thing1();
    }

    // 要这样
    if (
        foo === 123
        && bar === 'abc'
    ) {
        thing1();
    }

    // 要这样
    if (
        (foo === 123 || bar === 'abc')
        && doesItLookGoodWhenItBecomesThatLong()
        && isThisReallyHappening()
    ) {
        thing1();
    }

    // 要这样
    if (foo === 123 && bar === 'abc') {
        thing1();
    }
    ```
* 不要使用选择操作符代替控制语句

	```JavaScript
    // 别这样
    !isRunning && startRunning();

    // 要这样
    if (!isRunning) {
        startRunning();
    }
    ```
## 注释（Comments）
* 多行注释时使用 `/** ... */`

	```JavaScript
    // 别这样
    // make() returns a new element
    // based on the passed in tag name
    //
    // @param {String} tag
    // @return {Element} element
    function make(tag) {

        // ...

        return element;
    }

    // 要这样
    /**
    * make() returns a new element
    * based on the passed-in tag name
    */
    function make(tag) {

        // ...

        return element;
    }
    ```
* 使用 `//` 进行单行注释。将单行注释放在被评论主题的上方。在注释之前打一个空行，除非它位于代码块的第一行

	```JavaScript
    // 别这样
    const active = true;  // is current tab

    // 要这样
    // is current tab
    const active = true;

    // 别这样
    function getType() {
        console.log('fetching type...');
        // set the default type to 'no type'
        const type = this.type || 'no type';

        return type;
    }

    // 要这样
    function getType() {
        console.log('fetching type...');

        // set the default type to 'no type'
        const type = this.type || 'no type';

        return type;
    }

    // 这样也行
    function getType() {
        // set the default type to 'no type'
        const type = this.type || 'no type';

        return type;
    }
    ```
* 在每行注释的开头打一个空格，这更易读

	```JavaScript
    // 别这样
    //is current tab
    const active = true;

    // 要这样
    // is current tab
    const active = true;

    // 别这样
    /**
    *make() returns a new element
    *based on the passed-in tag name
    */
    function make(tag) {

        // ...

        return element;
    }

    // 要这样
    /**
    * make() returns a new element
    * based on the passed-in tag name
    */
    function make(tag) {

        // ...

        return element;
    }
    ```
* 使用 `FIXME` 或 `TODO` 为注释添加前缀可以帮助其他开发人员快速了解你关于此处的意图。这与常规注释不同，因为它们是可操作的。`FIXME: -- 需要搞清楚` 或 `TODO: -- 需要实现`
* 使用 `// FIXME:` 来注释问题

	```JavaScript
    class Calculator extends Abacus {
        constructor() {
            super();

            // FIXME: shouldn’t use a global here
            total = 0;
        }
    }
    ```
## 空格（Whitespace）
* 缩进用 2 个空格

	```JavaScript
    // 别这样
    function foo() {
    ∙∙∙∙let name;
    }

    // 别这样
    function bar() {
    ∙let name;
    }

    // 要这样
    function baz() {
    ∙∙let name;
    }
    ```
* 左花括号的左边打 1 个空格

	```JavaScript
    // 别这样
    function test(){
        console.log('test');
    }

    // 要这样
    function test() {
        console.log('test');
    }

    // 别这样
    dog.set('attr',{
        age: '1 year',
        breed: 'Bernese Mountain Dog',
    });

    // 要这样
    dog.set('attr', {
        age: '1 year',
        breed: 'Bernese Mountain Dog',
    });
    ```
* 在控制语句（`if`, `whilte` 等等）与左边的小括号直接打 1 个空格。函数的参数列表、函数名、函数调用和声明之间不要打空格

	```JavaScript
    // 别这样
    if(isJedi) {
        fight ();
    }

    // 要这样
    if (isJedi) {
        fight();
    }

    // 别这样
    function fight () {
        console.log ('Swooosh!');
    }

    // 要这样
    function fight() {
        console.log('Swooosh!');
    }
    ```
* 用空格隔开操作符

	```JavaScript
    // 别这样
    const x=y+5;

    // 要这样
    const x = y + 5;
    ```
* 结束文件时打一个换行

	```JavaScript
    // 别这样
    import { es6 } from './AirbnbStyleGuide';
    // ...
    export default es6;
    // 别这样
    import { es6 } from './AirbnbStyleGuide';
    // ...
    export default es6;↵
    ↵
    // 要这样
    import { es6 } from './AirbnbStyleGuide';
    // ...
    export default es6;↵
    ```
* 链式调用时时使用缩进（超过 2 个方法链）。把 `.` 放在前面，它强调该行是一个方法调用，而不是一条新的语句

	```JavaScript
    // 别这样
    $('#items').find('.selected').highlight().end().find('.open').updateCount();

    // 别这样
    $('#items').
    find('.selected').
        highlight().
        end().
    find('.open').
        updateCount();

    // 要这样
    $('#items')
    .find('.selected')
        .highlight()
        .end()
    .find('.open')
        .updateCount();

    // 别这样
    const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').classed('led', true)
        .attr('width', (radius + margin) * 2).append('svg:g')
        .attr('transform', `translate(${radius + margin},${radius + margin})`)
        .call(tron.led);

    // 要这样
    const leds = stage.selectAll('.led')
        .data(data)
        .enter().append('svg:svg')
        .classed('led', true)
        .attr('width', (radius + margin) * 2)
        .append('svg:g')
        .attr('transform', `translate(${radius + margin},${radius + margin})`)
        .call(tron.led);

    // 要这样
    const leds = stage.selectAll('.led').data(data);
    ```
* 在代码块和后面的语句之间打一个空行

	```JavaScript
    // 别这样
    if (foo) {
        return bar;
    }
    return baz;

    // 要这样
    if (foo) {
        return bar;
    }

    return baz;

    // 别这样
    const obj = {
        foo() {
        },
        bar() {
        },
    };
    return obj;

    // 要这样
    const obj = {
        foo() {
        },

        bar() {
        },
    };

    return obj;

    // 别这样
    const arr = [
        function foo() {
        },
        function bar() {
        },
    ];
    return arr;

    // 要这样
    const arr = [
        function foo() {
        },

        function bar() {
        },
    ];

    return arr;
    ```
* 不要用在代码块中填充空行

	```JavaScript
    // 别这样
    function bar() {

        console.log(foo);

    }

    // 别这样
    if (baz) {

        console.log(qux);
    } else {
        console.log(foo);

    }

    // 别这样
    class Foo {

        constructor(bar) {
            this.bar = bar;
        }
    }

    // 要这样
    function bar() {
        console.log(foo);
    }

    // 要这样
    if (baz) {
        console.log(qux);
    } else {
        console.log(foo);
    }
    ```
* 小括号里不要打空格

	```JavaScript
    // 别这样
    function bar( foo ) {
        return foo;
    }

    // 要这样
    function bar(foo) {
        return foo;
    }

    // 别这样
    if ( foo ) {
        console.log(foo);
    }

    // 要这样
    if (foo) {
        console.log(foo);
    }
    ```
* 中括号里不要打空格

	```JavaScript
    // 别这样
    const foo = [ 1, 2, 3 ];
    console.log(foo[ 0 ]);

    // 要这样
    const foo = [1, 2, 3];
    console.log(foo[0]);
    ```
* 在花括号里添加空格

	```JavaScript
    // 别这样
    const foo = {clark: 'kent'};

    // 要这样
    const foo = { clark: 'kent' };
    ```
* 避免有超过 100 个字符的代码行（包括空格）。注意：根据之前的规定，长字符串可免除此规则，不应分解
    > 为啥？这保证可读性和可维护性

	```JavaScript
    // 别这样
    const foo = jsonData && jsonData.foo && jsonData.foo.bar && jsonData.foo.bar.baz && jsonData.foo.bar.baz.quux && jsonData.foo.bar.baz.quux.xyzzy;

    // 别这样
    $.ajax({ method: 'POST', url: 'https://airbnb.com/', data: { name: 'John' } }).done(() => console.log('Congratulations!')).fail(() => console.log('You have failed this city.'));

    // 要这样
    const foo = jsonData
        && jsonData.foo
        && jsonData.foo.bar
        && jsonData.foo.bar.baz
        && jsonData.foo.bar.baz.quux
        && jsonData.foo.bar.baz.quux.xyzzy;

    // 要这样
    $.ajax({
        method: 'POST',
        url: 'https://airbnb.com/',
        data: { name: 'John' },
    })
        .done(() => console.log('Congratulations!'))
        .fail(() => console.log('You have failed this city.'));
    ```
## 逗号（Commas）
* 逗号放在行首： **不需要**

	```JavaScript
    // 别这样
    const story = [
        once
        , upon
        , aTime
    ];

    // 要这样
    const story = [
        once,
        upon,
        aTime,
    ];

    // 别这样
    const hero = {
        firstName: 'Ada'
        , lastName: 'Lovelace'
        , birthYear: 1815
        , superPower: 'computers'
    };

    // 要这样
    const hero = {
        firstName: 'Ada',
        lastName: 'Lovelace',
        birthYear: 1815,
        superPower: 'computers',
    };
    ```
* 增加结尾处的逗号：**对**
    > 为啥？这让 git diffs 更清晰。Babel 等编译器在编译代码时会移除尾部的逗号，这意味着你不需要担心古老浏览器中的尾部逗号问题

	```JavaScript
    // 别这样 - git diff without trailing comma
    const hero = {
         firstName: 'Florence',
    -    lastName: 'Nightingale'
    +    lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing']
    };

    // 要这样 - git diff with trailing comma
    const hero = {
         firstName: 'Florence',
         lastName: 'Nightingale',
    +    inventorOf: ['coxcomb chart', 'modern nursing'],
    };
    ```

	```JavaScript
    // 别这样
    const hero = {
        firstName: 'Dana',
        lastName: 'Scully'
    };

    const heroes = [
        'Batman',
        'Superman'
    ];

    // 要这样
    const hero = {
        firstName: 'Dana',
        lastName: 'Scully',
    };

    const heroes = [
        'Batman',
        'Superman',
    ];

    // 别专业
    function createHero(
        firstName,
        lastName,
        inventorOf
    ) {
        // does nothing
    }

    // 要这样
    function createHero(
        firstName,
        lastName,
        inventorOf,
    ) {
        // does nothing
    }

    // 要这样 (note that a comma must not appear after a "rest" element)
    function createHero(
        firstName,
        lastName,
        inventorOf,
        ...heroArgs
    ) {
    // does nothing
    }

    // 别这样
    createHero(
        firstName,
        lastName,
        inventorOf
    );

    // 要这样
    createHero(
        firstName,
        lastName,
        inventorOf,
    );

    // 要这样 (note that a comma must not appear after a "rest" element)
    createHero(
        firstName,
        lastName,
        inventorOf,
        ...heroArgs
    );
    ```
## 分号（Semicolons）
* **用!**
    > 为啥？当 JavaScript 遇到没有分号的换行符时，它使用一组称为 Automatic Semicolon Insertion 的规则来确定它是否应该将换行符视为语句的结尾，并且（如名称所示）将分号放入它所认为的一行代码的结束之处。但是，ASI 包含一些古怪的行为，如果 JavaScript 错误地解释了换行符，你的代码将会打断。随着 JavaScript 增加越来越多的新功能，这些规则将变得更加复杂。所以自己乖乖打分号并配置好 linter 以捕获缺少分号的语句，将有助于防止遇到古怪问题

	```JavaScript
    // 别这样 - raises exception
    const luke = {}
    const leia = {}
    [luke, leia].forEach(jedi => jedi.father = 'vader')

    // 别这样 - raises exception
    const reaction = "No! That's impossible!"
    (async function meanwhileOnTheFalcon() {
        // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
        // ...
    }())

    // 别这样 - returns `undefined` instead of the value on the next line - always happens when `return` is on a line by itself because of ASI!
    function foo() {
        return
            'search your feelings, you know it to be foo'
    }

    // 要这样
    const luke = {};
    const leia = {};
    [luke, leia].forEach((jedi) => {
        jedi.father = 'vader';
    });

    // 要这样
    const reaction = "No! That's impossible!";
    (async function meanwhileOnTheFalcon() {
        // handle `leia`, `lando`, `chewie`, `r2`, `c3p0`
        // ...
    }());

    // 要这样
    function foo() {
    return 'search your feelings, you know it to be foo';
    }
    ```
## 类型转换和强制转换（Type Casting & Coercion）
* 在语句开头执行类型转换
* 字符串：

	```JavaScript
    // => this.reviewScore = 9;

    // 别这样，调用 typeof 显示是 object 而不是 string
    const totalScore = new String(this.reviewScore);

    // 别这样，调用 this.reviewScore.valueOf()
    const totalScore = this.reviewScore + '';

    // 别这样，不保证返回一个字符串
    const totalScore = this.reviewScore.toString();

    // 别这样
    const totalScore = String(this.reviewScore);
    ```
* 数字：使用 `Number` 来类型转换，解析字符串时使用 `parseInt`

	```JavaScript
    const inputValue = '4';

    // 别这样
    const val = new Number(inputValue);

    // 别这样
    const val = +inputValue;

    // 别这样
    const val = inputValue >> 0;

    // 别这样
    const val = parseInt(inputValue);

    // 要这样
    const val = Number(inputValue);

    // 要这样
    const val = parseInt(inputValue, 10);
    ```
* 如果无论出于何种原因，你正在做一些狂野的事情，并且 `parseInt` 是你的瓶颈，并且出于性能原因需要使用位移操作符，请留下注释来解释为什么和做什么

	```JavaScript
    // 要这样
    /**
    * parseInt was the reason my code was slow.
    * Bitshifting the String to coerce it to a
    * Number made it a lot faster.
    */
    const val = inputValue >> 0;
    ```
* 注意：使用位移操作符时要小心。`Number` 表示为 64 位值，但位移操作符始终返回 32 位整数。对于大于 32 位的整数值，位移操作可能会导致意外行为。最大的有符号 32 位数是 2,147,483,647

	```JavaScript
    2147483647 >> 0; // => 2147483647
    2147483648 >> 0; // => -2147483648
    2147483649 >> 0; // => -2147483647
    ```
* 布尔值：

	```JavaScript
    const age = 0;

    // 别这样
    const hasAge = new Boolean(age);

    // 要这样
    const hasAge = Boolean(age);

    // 最佳
    const hasAge = !!age;
    ```
## 命名约定（Naming Conventions）
* 避免单一字母的名称。命名要可以描述你定义的东西

	```JavaScript
    // 别这样
    function q() {
        // ...
    }

    // 要这样
    function query() {
        // ...
    }
    ```
* 为对象、函数、实例命名时遵循驼峰命名规则

	```JavaScript
    // 别这样
    const OBJEcttsssss = {};
    const this_is_my_object = {};
    function c() {}

    // 要这样
    const thisIsMyObject = {};
    function thisIsMyFunction() {}
    ```
* 只有在给类的构造器命名时使用帕斯卡命名规则

	```JavaScript
    // 别这样
    function user(options) {
        this.name = options.name;
    }

    const bad = new user({
        name: 'nope',
    });

    // 要这样
    class User {
        constructor(options) {
            this.name = options.name;
        }
    }

    const good = new User({
        name: 'yup',
    });
    ```
* 别在首尾使用下划线
    > 为啥？JavaScript 在属性、方法上没有 private 的概念。虽然在名称最前面加下划线通常表示这是一个 private 的东西，但事实上却是 public 的。这种约定可能会导致开发人员错误地认为更改不会被视为中断，或者不需要测试

	```JavaScript
    // 别这样
    this.__firstName__ = 'Panda';
    this.firstName_ = 'Panda';
    this._firstName = 'Panda';

    // 要这样
    this.firstName = 'Panda';

    // 要这样, in environments where WeakMaps are available
    // see https://kangax.github.io/compat-table/es6/#test-WeakMap
    const firstNames = new WeakMap();
    firstNames.set(this, 'Panda');
    ```
* 不要保存 `this` 的引用，使用箭头函数或者 `Function#bind`

	```JavaScript
    // 别这样
    function foo() {
        const self = this;
        return function () {
            console.log(self);
        };
    }

    // 别这样
    function foo() {
        const that = this;
        return function () {
            console.log(that);
        };
    }

    // 要这样
    function foo() {
        return () => {
            console.log(this);
        };
    }
    ```
* 基本的文件名应该与默认导出的名字完全匹配

	```JavaScript
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
    // 别这样
    import CheckBox from './checkBox'; // PascalCase import/export, camelCase filename
    import FortyTwo from './FortyTwo'; // PascalCase import/filename, camelCase export
    import InsideDirectory from './InsideDirectory'; // PascalCase import/filename, camelCase export

    // 别这样
    import CheckBox from './check_box'; // PascalCase import/export, snake_case filename
    import forty_two from './forty_two'; // snake_case import/filename, camelCase export
    import inside_directory from './inside_directory'; // snake_case import, camelCase export
    import index from './inside_directory/index'; // requiring the index file explicitly
    import insideDirectory from './insideDirectory/index'; // requiring the index file explicitly

    // 要这样
    import CheckBox from './CheckBox'; // PascalCase export/import/filename
    import fortyTwo from './fortyTwo'; // camelCase export/import/filename
    import insideDirectory from './insideDirectory'; // camelCase export/import/directory name/implicit "index"
    // ^ supports both insideDirectory.js and insideDirectory/index.js
    ```
* 导出默认函数时取名应遵循驼峰命名规则。你的文件名和函数名应该保持一致

	```JavaScript
    function makeStyleGuide() {
        // ...
    }

    export default makeStyleGuide;
    ```
* 当你导出一个构造器/ 类/ 单例/ 函数库/ 裸对象时遵循帕斯卡命名规则

	```JavaScript
    const AirbnbStyleGuide = {
        es6: {
        },
    };

    export default AirbnbStyleGuide;
    ```
* 首字母缩略词和首字母缩写应始终全部大写，或全部小写

	```JavaScript
    // 别这样
    import SmsContainer from './containers/SmsContainer';

    // 别这样
    const HttpRequests = [
        // ...
    ];

    // 要这样
    import SMSContainer from './containers/SMSContainer';

    // 要这样
    const HTTPRequests = [
    // ...
    ];

    // 也不错
    const httpRequests = [
    // ...
    ];

    // 最佳
    import TextMessageContainer from './containers/TextMessageContainer';

    // 最佳
    const requests = [
        // ...
    ];
    ```
* 以下几种情况你可以将一个常量大写：
    * 用于导出
    * 是 `const`（不能被重新赋值）
    * 程序员认定它（以及它的嵌套属性）不会改变
    > 为啥？这是一个额外的工具，可以帮助程序员知道变量是否会发生变化。 全大写的变量让程序员知道他们可以相信这变量（及其属性）不会发生改变
    * 所有 `const` 都要这样？这不是必须的，所以不应该将全大写格式用于文件中的常量。但是，它应该用于导出的产量
    * 对于导出的对象呢？大写位于导出顶级的部分（例如 EXPORTED_OBJECT.key），并保持所有嵌套属性不会更改

	```JavaScript
    // 别这样
    const PRIVATE_VARIABLE = 'should not be unnecessarily uppercased within a file';

    // 别这样
    export const THING_TO_BE_CHANGED = 'should obviously not be uppercased';

    // 别这样
    export let REASSIGNABLE_VARIABLE = 'do not use let with uppercase variables';

    // ---

    // allowed but does not supply semantic value
    export const apiKey = 'SOMEKEY';

    // better in most cases
    export const API_KEY = 'SOMEKEY';

    // ---

    // 别这样 - unnecessarily uppercases key while adding no semantic value
    export const MAPPING = {
        KEY: 'value'
    };

    // 要这样
    export const MAPPING = {
        key: 'value'
    };
    ```
## 访问器（Accessors）
* 属性的访问器方法不是必须的
* 不要使用 JavaScript getters / setter，因为它们会导致意想不到的副作用，而且更难以测试、维护和推理。相反，如果你做访问器，使用 `getVal()` 和 `setVal('hello')`

	```JavaScript
    // 别这样
    class Dragon {
        get age() {
            // ...
        }

        set age(value) {
            // ...
        }
    }

    // 要这样
    class Dragon {
        getAge() {
            // ...
        }

        setAge(value) {
            // ...
        }
    }
    ```
* 如果一个属性的类型或者方法的返回类型是 `boolean`，使用 `isVal()` 或者 `hasVal()`

	```JavaScript
    // 别这样
    if (!dragon.age()) {
        return false;
    }

    // 要这样
    if (!dragon.hasAge()) {
        return false;
    }
    ```
* 创建 `get()` 和 `set()` 方法也是可以的，但要保持一致性

	```JavaScript
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
## 事件（Events）
* 将数据附加到事件（无论是 DOM 事件还是诸如 Backbone 事件的更专有的东西）时，传递对象字面量（也称为 “hash”）而不是原始值。 这允许后续贡献者向事件添加更多数据，而无需查找和更新事件的每个处理程序，例如，不要这样做：

	```JavaScript
    // 别这样
    $(this).trigger('listingUpdated', listing.id);

    // ...

    $(this).on('listingUpdated', (e, listingID) => {
        // do something with listingID
    });
    ```
    这样做更好：

	```JavaScript
    // 要这样
    $(this).trigger('listingUpdated', { listingID: listing.id });

    // ...

    $(this).on('listingUpdated', (e, data) => {
        // do something with data.listingID
    });
    ```
## jQuery
* 使用 `$` 前缀 jQuery 对象变量

	```JavaScript
    // 别这样
    const sidebar = $('.sidebar');

    // 要这样
    const $sidebar = $('.sidebar');

    // 要这样
    const $sidebarBtn = $('.sidebar-btn');
    ```
* 缓存 jQuery 的查找结果

	```JavaScript
    // 别这样
    function setSidebar() {
        $('.sidebar').hide();

        // ...

        $('.sidebar').css({
            'background-color': 'pink',
        });
    }

    // 要这样
    function setSidebar() {
        const $sidebar = $('.sidebar');
        $sidebar.hide();

        // ...

        $sidebar.css({
            'background-color': 'pink',
        });
    }
    ```
* DOM 查询时使用级联 `$('.sidebar ul')` 或者 parent > child `$('.sidebar > ul')`
* 对有作用域的 jQuery 对象使用 `find` 进行查询

	```JavaScript
    // 别这样
    $('ul', '.sidebar').hide();

    // 别这样
    $('.sidebar').find('ul').hide();

    // 要这样
    $('.sidebar ul').hide();

    // 要这样
    $('.sidebar > ul').hide();

    // 要这样
    $sidebar.find('ul').hide();
    ```
> 以下章节内容全是链接，暂未翻译
## ECMAScript 5 Compatibility
## ECMAScript 6+ (ES 2015+) Styles
## Standard Library
## Testing
## Performance
## Resources

