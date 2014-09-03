# Airbnb JavaScript 书写指南() {

*通往 JavaScript 的阳光大道*


## 目录

  1. [类型](#类型)
  1. [对象](#对象)
  1. [数组](#数组)
  1. [字符串](#字符串)
  1. [函数](#函数)
  1. [属性](#属性)
  1. [变量](#变量)
  1. [提前](#提前)
  1. [条件表达式 & 比较](#条件表达式--比较)
  1. [块](#块)
  1. [注释](#注释)
  1. [空白符](#空白符)
  1. [逗号](#逗号)
  1. [分号](#分号)
  1. [类型显式转换 & 类型隐式转换](#类型显式转换--类型隐式转换)
  1. [命名约定](#命名约定)
  1. [存取器](#存取器)
  1. [构造器](#构造器)
  1. [事件](#事件)
  1. [模块](#模块)
  1. [jQuery](#jquery)
  1. [ECMAScript 5 兼容性](#ecmascript-5-兼容性)
  1. [测试](#测试)
  1. [性能](#性能)
  1. [资源](#资源)
  1. [其他](#其他)
  1. [翻译](#翻译)
  1. [JavaScript 书写指南的指南](#javascript-书写指南的指南)
  1. [贡献者](#贡献者)
  1. [授权条款](#授权条款)

## 类型

  - **原始类型**: 访问原始类型时，直接操作它的值

    + `string`
    + `number`
    + `boolean`
    + `null`
    + `undefined`

    ```javascript
    var foo = 1,
        bar = foo;

    bar = 9;

    console.log(foo, bar); // => 1, 9
    ```
  - **复合类型**: 访问复合类型时，操作的是指向它的值的一个引用

    + `object`
    + `array`
    + `function`

    ```javascript
    var foo = [1, 2],
        bar = foo;

    bar[0] = 9;

    console.log(foo[0], bar[0]); // => 9, 9
    ```

**[⬆ 返回顶部](#目录)**

## 对象

  - 使用字面量语法创建对象

    ```javascript
    // 劣
    var item = new Object();

    // 优
    var item = {};
    ```

  - 不要用[保留字](http://es5.github.io/#x7.6.1)作键名。IE8 下不可行。[更多详情](https://github.com/airbnb/javascript/issues/61)

    ```javascript
    // 劣
    var superman = {
      default: { clark: 'kent' },
      private: true
    };

    // 优
    var superman = {
      defaults: { clark: 'kent' },
      hidden: true
    };
    ```

  - 用可读的同义词替换保留字

    ```javascript
    // 劣
    var superman = {
      class: 'alien'
    };

    // 劣
    var superman = {
      klass: 'alien'
    };

    // 优
    var superman = {
      type: 'alien'
    };
    ```

**[⬆ 返回顶部](#目录)**

## 数组

  - 用字面量语法创建数组

    ```javascript
    // 劣
    var items = new Array();

    // 优
    var items = [];
    ```

  - 如果不清楚数组长度，请用 Array#push 方法

    ```javascript
    var someStack = [];


    // 劣
    someStack[someStack.length] = 'abracadabra';

    // 优
    someStack.push('abracadabra');
    ```

  - 需要拷贝数组的话，使用 Array#slice。 [jsPerf](http://jsperf.com/converting-arguments-to-an-array/7)

    ```javascript
    var len = items.length,
        itemsCopy = [],
        i;

    // 劣
    for (i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }

    // 优
    itemsCopy = items.slice();
    ```

  - 要转换一个类数组的对象成真正数组，使用 Array#slice

    ```javascript
    function trigger() {
      var args = Array.prototype.slice.call(arguments);
      ...
    }
    ```

**[⬆ 返回顶部](#目录)**


## 字符串

  - 字符串用单引号 `''`

    ```javascript
    // 劣
    var name = "Bob Parr";

    // 优
    var name = 'Bob Parr';

    // 劣
    var fullName = "Bob " + this.lastName;

    // 优
    var fullName = 'Bob ' + this.lastName;
    ```

  - 超过80个字符的字符串应该放多行写，然后用字符串连接符
  - 注意：如果过度使用字符串连接符，会影响到性能。[jsPerf](http://jsperf.com/ya-string-concat) & [讨论](https://github.com/airbnb/javascript/issues/40)

    ```javascript
    // 劣
    var errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';

    // 劣
    var errorMessage = 'This is a super long error that was thrown because \
    of Batman. When you stop to think about how Batman had anything to do \
    with this, you would get nowhere \
    fast.';

    // 优
    var errorMessage = 'This is a super long error that was thrown because ' +
      'of Batman. When you stop to think about how Batman had anything to do ' +
      'with this, you would get nowhere fast.';
    ```

  - 动态构建字符串时，使用 Array#join 替代字符串连连。主要针对 IE：[jsPerf](http://jsperf.com/string-vs-array-concat/2)

    ```javascript
    var items,
        messages,
        length,
        i;

    messages = [{
      state: 'success',
      message: 'This one worked.'
    }, {
      state: 'success',
      message: 'This one worked as well.'
    }, {
      state: 'error',
      message: 'This one did not work.'
    }];

    length = messages.length;

    // 劣
    function inbox(messages) {
      items = '<ul>';

      for (i = 0; i < length; i++) {
        items += '<li>' + messages[i].message + '</li>';
      }

      return items + '</ul>';
    }

    // 优
    function inbox(messages) {
      items = [];

      for (i = 0; i < length; i++) {
        items[i] = messages[i].message;
      }

      return '<ul><li>' + items.join('</li><li>') + '</li></ul>';
    }
    ```

**[⬆ 返回顶部](#目录)**


## 函数

  - 函数表达式：

    ```javascript
    // 匿名函数表达式
    var anonymous = function() {
      return true;
    };

    // 具名函数表达式
    var named = function named() {
      return true;
    };

    // 立即执行函数表达式(IIFE)
    (function() {
      console.log('Welcome to the Internet. Please follow me.');
    })();
    ```

  - 不要在非函数块(if, while, etc)中声明函数。改为将函数赋给变量。浏览器允许你在非函数块里声明函数，但它们的解释各异，这相当糟心
  - **注意：** ECMA-262 定义 `block` 为一系列的声明语句。函数定义不是声明语句。[ECMA-262 在该问题上的说明](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97)。

    ```javascript
    // 劣
    if (currentUser) {
      function test() {
        console.log('Nope.');
      }
    }

    // 优
    var test;
    if (currentUser) {
      test = function test() {
        console.log('Yup.');
      };
    }
    ```

  - 不要给参数命名 `arguments`，否则它会覆盖每个函数作用域中的 `arguments` 对象

    ```javascript
    // 劣
    function nope(name, options, arguments) {
      // ...stuff...
    }

    // 优
    function yup(name, options, args) {
      // ...stuff...
    }
    ```

**[⬆ 返回顶部](#目录)**



## 属性

  - 访问属性时使用点号

    ```javascript
    var luke = {
      jedi: true,
      age: 28
    };

    // 劣
    var isJedi = luke['jedi'];

    // 优
    var isJedi = luke.jedi;
    ```

  - 属性名为变量时，使用下标`[]`访问属性

    ```javascript
    var luke = {
      jedi: true,
      age: 28
    };

    function getProp(prop) {
      return luke[prop];
    }

    var isJedi = getProp('jedi');
    ```

**[⬆ 返回顶部](#目录)**


## 变量

  - 声明变量切记要用 `var`。不这样的话会暴露全局变量。我们要试图避免污染全局命名空间。地球超人警告过我们。

    ```javascript
    // 劣
    superPower = new SuperPower();

    // 优
    var superPower = new SuperPower();
    ```

  - 声明多个变量，只用一个 `var`，然后每行声明一个变量

    ```javascript
    // 劣
    var items = getItems();
    var goSportsTeam = true;
    var dragonball = 'z';

    // 优
    var items = getItems(),
        goSportsTeam = true,
        dragonball = 'z';
    ```

  - 未赋值的变量放到最后声明。也许你后面要依赖已赋值的变量给变量赋值，这会十分有用。

    ```javascript
    // 劣
    var i, len, dragonball,
        items = getItems(),
        goSportsTeam = true;

    // 劣
    var i, items = getItems(),
        dragonball,
        goSportsTeam = true,
        len;

    // 优
    var items = getItems(),
        goSportsTeam = true,
        dragonball,
        length,
        i;
    ```

  - 在变量作用域顶部声明变量。这能避免变量声明和赋值的提前相关的问题。

    ```javascript
    // 劣
    function() {
      test();
      console.log('doing stuff..');

      //..other stuff..

      var name = getName();

      if (name === 'test') {
        return false;
      }

      return name;
    }

    // 优
    function() {
      var name = getName();

      test();
      console.log('doing stuff..');

      //..other stuff..

      if (name === 'test') {
        return false;
      }

      return name;
    }

    // 劣
    function() {
      var name = getName();

      if (!arguments.length) {
        return false;
      }

      return true;
    }

    // 优
    function() {
      if (!arguments.length) {
        return false;
      }

      var name = getName();

      return true;
    }
    ```

**[⬆ 返回顶部](#目录)**


## 提前

  - 变量声明被提前到它们的作用域顶部，但赋值行为并没有一样待遇。

    ```javascript
    // 我们知道这有问题(假定没有 notDefined 全局变量)
    function example() {
      console.log(notDefined); // => 抛出 ReferenceError 错误
    }

    // 在你引用变量的后头创建一个变量声明则可以
    // 这是因为变量提前的缘故。
    // 注意：赋值 `true` 的行为没有提前
    function example() {
      console.log(declaredButNotAssigned); // => undefined
      var declaredButNotAssigned = true;
    }

    // 编译器将变量声明提前到作用域顶部。
    // 我们的例子可以重写成：
    function example() {
      var declaredButNotAssigned;
      console.log(declaredButNotAssigned); // => undefined
      declaredButNotAssigned = true;
    }
    ```

  - 匿名函数表达式会提前它们的变量名，所赋的函数体则不提前。

    ```javascript
    function example() {
      console.log(anonymous); // => undefined

      anonymous(); // => TypeError anonymous is not a function

      var anonymous = function() {
        console.log('anonymous function expression');
      };
    }
    ```

  - 具名函数表达式会提前变量名，函数名和函数体不提前。

    ```javascript
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      superPower(); // => ReferenceError superPower is not defined

      var named = function superPower() {
        console.log('Flying');
      };
    }

    // 函数名与变量名一致时结果是一样的。
    function example() {
      console.log(named); // => undefined

      named(); // => TypeError named is not a function

      var named = function named() {
        console.log('named');
      }
    }
    ```

  - 函数声明提前它们的函数名和函数体。

    ```javascript
    function example() {
      superPower(); // => Flying

      function superPower() {
        console.log('Flying');
      }
    }
    ```

  - 更多请点击 [Ben Cherry](http://www.adequatelygood.com/) 写的 [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting)

**[⬆ 返回顶部](#目录)**



## 条件表达式 & 比较

  - 使用 `===` 和 `!==` 而不是 `==` 和 `!=`。
  - 条件表达式会使用 `ToBoolean` 方法强制转换类型，简单原则如下：

    + **Objects** 类型取值 **true**
    + **Undefined** 类型取值 **false**
    + **Null** 类型取值 **false**
    + **Booleans** 类型取值 **boolean 的值**
    + **Numbers** 类型在值为 **+0, -0, 或 NaN** 时取值 **false**，否则取值 **true**
    + **Strings** 类型在值为空字符串 `''` 时取值 **false** ，否则取值 **true**

    ```javascript
    if ([0]) {
      // true
      // 数组是对象，对象类型转换取值 true
    }
    ```

  - 抄近路。

    ```javascript
    // 劣
    if (name !== '') {
      // ...stuff...
    }

    // 优
    if (name) {
      // ...stuff...
    }

    // 劣
    if (collection.length > 0) {
      // ...stuff...
    }

    // 优
    if (collection.length) {
      // ...stuff...
    }
    ```

  - 更多信息请看 Angus Croll 的 [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108)。

**[⬆ 返回顶部](#目录)**


## 块

  - 所有多行的块都要用花括号

    ```javascript
    // 劣
    if (test)
      return false;

    // 优
    if (test) return false;

    // 优
    if (test) {
      return false;
    }

    // 劣
    function() { return false; }

    // 优
    function() {
      return false;
    }
    ```

**[⬆ 返回顶部](#目录)**


## 注释

  - 多行注释使用 `/** ... */`。加上描述，明确类型及所有变量的值和返回值。

    ```javascript
    // 劣
    // make() returns a new element
    // based on the passed in tag name
    //
    // @param <String> tag
    // @return <Element> element
    function make(tag) {

      // ...stuff...

      return element;
    }

    // 优
    /**
     * make() returns a new element
     * based on the passed in tag name
     *
     * @param <String> tag
     * @return <Element> element
     */
    function make(tag) {

      // ...stuff...

      return element;
    }
    ```

  - 单行注释使用 `//`。注释写在注释对象上一行。在注释行前加一空行。

    ```javascript
    // 劣
    var active = true;  // is current tab

    // 优
    // is current tab
    var active = true;

    // 劣
    function getType() {
      console.log('fetching type...');
      // set the default type to 'no type'
      var type = this._type || 'no type';

      return type;
    }

    // 优
    function getType() {
      console.log('fetching type...');

      // set the default type to 'no type'
      var type = this._type || 'no type';

      return type;
    }
    ```

  - 注释前加 `FIXME` 或 `TODO`，帮助他人快速理解，你这是在指出一个需要解决的问题，或是提了一个解决办法，只是有待实施。这些与普通注释不一样，因为它们是可付诸行动的。行动为 `FIXME -- 需要解决它`或是 `TODO - 有待实施`。

  - 使用 `// FIXME:` 来标识问题

    ```javascript
    function Calculator() {

      // FIXME: 不应使用全局变量
      total = 0;

      return this;
    }
    ```

  - 使用 `// TODO:` 来标识问题解决方案

    ```javascript
    function Calculator() {

      // TODO: total 应能通过参数选项来配置
      this.total = 0;

      return this;
    }
  ```

**[⬆ 返回顶部](#目录)**


## 空白符

  - 设置软 tab 键为 2 个空格

    ```javascript
    // 劣
    function() {
    ∙∙∙∙var name;
    }

    // 劣
    function() {
    ∙var name;
    }

    // 优
    function() {
    ∙∙var name;
    }
    ```

  - 在起始花括号前加一个空格

    ```javascript
    // 劣
    function test(){
      console.log('test');
    }

    // 优
    function test() {
      console.log('test');
    }

    // 劣
    dog.set('attr',{
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    });

    // 优
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    });
    ```

  - 加空白衬操作符。

    ```javascript
    // 劣
    var x=y+5;

    // 优
    var x = y + 5;
    ```

  - 文件末以一个换行符结束。

    ```javascript
    // 劣
    (function(global) {
      // ...stuff...
    })(this);
    ```

    ```javascript
    // 劣
    (function(global) {
      // ...stuff...
    })(this);↵
    ↵
    ```

    ```javascript
    // 优
    (function(global) {
      // ...stuff...
    })(this);↵
    ```

  - 长链的方法用缩进。

    ```javascript
    // 劣
    $('#items').find('.selected').highlight().end().find('.open').updateCount();

    // 优
    $('#items')
      .find('.selected')
        .highlight()
        .end()
      .find('.open')
        .updateCount();

    // 劣
    var leds = stage.selectAll('.led').data(data).enter().append('svg:svg').class('led', true)
        .attr('width',  (radius + margin) * 2).append('svg:g')
        .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
        .call(tron.led);

    // 优
    var leds = stage.selectAll('.led')
        .data(data)
      .enter().append('svg:svg')
        .class('led', true)
        .attr('width',  (radius + margin) * 2)
      .append('svg:g')
        .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
        .call(tron.led);
    ```

**[⬆ 返回顶部](#目录)**

## 逗号

  - 开头加逗号： **别。**

    ```javascript
    // 劣
    var once
      , upon
      , aTime;

    // 优
    var once,
        upon,
        aTime;

    // 劣
    var hero = {
        firstName: 'Bob'
      , lastName: 'Parr'
      , heroName: 'Mr. Incredible'
      , superPower: 'strength'
    };

    // 优
    var hero = {
      firstName: 'Bob',
      lastName: 'Parr',
      heroName: 'Mr. Incredible',
      superPower: 'strength'
    };
    ```

  - 末尾额外的逗号： **别。** IE6/7 和 IE9 的怪异模式会出问题。另外，ES3 的某些实施里，数组如果有额外逗号，会增长数组的长度值。ES5 ([source](http://es5.github.io/#D)) 里则澄清了：

  > 版本 5 明确表示数组末尾的逗号不会增长数组长度值。从语义上说，这跟版本 3 其实没差别，只是版本 3 的某些实施此前可能误解该涵义。

    ```javascript
    // 劣
    var hero = {
      firstName: 'Kevin',
      lastName: 'Flynn',
    };

    var heroes = [
      'Batman',
      'Superman',
    ];

    // 优
    var hero = {
      firstName: 'Kevin',
      lastName: 'Flynn'
    };

    var heroes = [
      'Batman',
      'Superman'
    ];
    ```

**[⬆ 返回顶部](#目录)**


## 分号

  - **要。**

    ```javascript
    // 劣
    (function() {
      var name = 'Skywalker'
      return name
    })()

    // 优
    (function() {
      var name = 'Skywalker';
      return name;
    })();

    // 优 (guards against the function becoming an argument when two files with IIFEs are concatenated)
    ;(function() {
      var name = 'Skywalker';
      return name;
    })();
    ```

    [阅读更多](http://stackoverflow.com/a/7365214/1712802).

**[⬆ 返回顶部](#目录)**


## 类型显式转换 & 类型隐式转换

  - 把类型隐式转换放到声明语句开头。
  - 字符串：

    ```javascript
    //  => this.reviewScore = 9;

    // 劣
    var totalScore = this.reviewScore + '';

    // 优
    var totalScore = '' + this.reviewScore;

    // 劣
    var totalScore = '' + this.reviewScore + ' total score';

    // 优
    var totalScore = this.reviewScore + ' total score';
    ```

  - 使用 `parseInt` 转换数值型，且要指明基数。

    ```javascript
    var inputValue = '4';

    // 劣
    var val = new Number(inputValue);

    // 劣
    var val = +inputValue;

    // 劣
    var val = inputValue >> 0;

    // 劣
    var val = parseInt(inputValue);

    // 优
    var val = Number(inputValue);

    // 优
    var val = parseInt(inputValue, 10);
    ```

  - 且不论你出于什么原因在做某些疯狂的事情然后 `parseInt` 是你瓶颈处，出于[性能原因](http://jsperf.com/coercion-vs-casting/3)需要按位移位，欢迎留言解释为什么，以及你在做什么。

    ```javascript
    // 优
    /**
     * parseInt was the reason my code was slow.
     * Bitshifting the String to coerce it to a
     * Number made it a lot faster.
     */
    var val = inputValue >> 0;
    ```

  - **注意：** 在使用按位移位操作时请谨慎。数值是 [64-bit 的值](http://es5.github.io/#x4.3.19) ，而按位移位操作返回的总是 32-bit 的整数([来源](http://es5.github.io/#x11.7))。对大于 32 bits 的整数，按位移位会导致不可预料的情况。[讨论](https://github.com/airbnb/javascript/issues/109)。最大的 32-bit 正整数值是 2,147,483,647：

    ```javascript
    2147483647 >> 0 //=> 2147483647
    2147483648 >> 0 //=> -2147483648
    2147483649 >> 0 //=> -2147483647
    ```

  - 布尔类型：

    ```javascript
    var age = 0;

    // 劣
    var hasAge = new Boolean(age);

    // 优
    var hasAge = Boolean(age);

    // 优
    var hasAge = !!age;
    ```

**[⬆ 返回顶部](#目录)**


## 命名约定

  - 避免单字母名称。命名应具描述性。

    ```javascript
    // 劣
    function q() {
      // ...stuff...
    }

    // 优
    function query() {
      // ..stuff..
    }
    ```

  - 命名对象，函数与实例时使用驼峰拼写

    ```javascript
    // 劣
    var OBJEcttsssss = {};
    var this_is_my_object = {};
    function c() {}
    var u = new user({
      name: 'Bob Parr'
    });

    // 优
    var thisIsMyObject = {};
    function thisIsMyFunction() {}
    var user = new User({
      name: 'Bob Parr'
    });
    ```

  - 命名构造器或类时使用帕斯卡拼写

    ```javascript
    // 劣
    function user(options) {
      this.name = options.name;
    }

    var bad = new user({
      name: 'nope'
    });

    // 优
    function User(options) {
      this.name = options.name;
    }

    var good = new User({
      name: 'yup'
    });
    ```

  - 命名私有属性时加下划线 `_` 前缀

    ```javascript
    // 劣
    this.__firstName__ = 'Panda';
    this.firstName_ = 'Panda';

    // 优
    this._firstName = 'Panda';
    ```

  - 使用 `_this` 来保存 `this` 引用

    ```javascript
    // 劣
    function() {
      var self = this;
      return function() {
        console.log(self);
      };
    }

    // 劣
    function() {
      var that = this;
      return function() {
        console.log(that);
      };
    }

    // 优
    function() {
      var _this = this;
      return function() {
        console.log(_this);
      };
    }
    ```

  - 给函数命名。有助于追踪堆栈。

    ```javascript
    // 劣
    var log = function(msg) {
      console.log(msg);
    };

    // 优
    var log = function log(msg) {
      console.log(msg);
    };
    ```

  - **注意：** IE8 及以下版本里具名函数有些怪异。更多请看 [http://kangax.github.io/nfe/](http://kangax.github.io/nfe/)。

**[⬆ 返回顶部](#目录)**


## 存取器

  - 不必要给属性写存取器函数
  - 如果你确定要写，则使用 getVal() 和 setVal('hello')

    ```javascript
    // 劣
    dragon.age();

    // 优
    dragon.getAge();

    // 劣
    dragon.age(25);

    // 优
    dragon.setAge(25);
    ```

  - 如果属性是布尔值，则使用 isVal() 或 hasVal()

    ```javascript
    // 劣
    if (!dragon.age()) {
      return false;
    }

    // 优
    if (!dragon.hasAge()) {
      return false;
    }
    ```

  - 创建 get() 和 set() 函数也没问题，但记得要一致

    ```javascript
    function Jedi(options) {
      options || (options = {});
      var lightsaber = options.lightsaber || 'blue';
      this.set('lightsaber', lightsaber);
    }

    Jedi.prototype.set = function(key, val) {
      this[key] = val;
    };

    Jedi.prototype.get = function(key) {
      return this[key];
    };
    ```

**[⬆ 返回顶部](#目录)**


## 构造器

  - 将方法赋给原型对象，而不是用新对象覆盖原型。覆盖原型使得继承不能用：因为重设原型你就重写了基类！

    ```javascript
    function Jedi() {
      console.log('new jedi');
    }

    // 劣
    Jedi.prototype = {
      fight: function fight() {
        console.log('fighting');
      },

      block: function block() {
        console.log('blocking');
      }
    };

    // 优
    Jedi.prototype.fight = function fight() {
      console.log('fighting');
    };

    Jedi.prototype.block = function block() {
      console.log('blocking');
    };
    ```

  - 方法可以返回 `this`，有助于链式使用方法

    ```javascript
    // 劣
    Jedi.prototype.jump = function() {
      this.jumping = true;
      return true;
    };

    Jedi.prototype.setHeight = function(height) {
      this.height = height;
    };

    var luke = new Jedi();
    luke.jump(); // => true
    luke.setHeight(20) // => undefined

    // 优
    Jedi.prototype.jump = function() {
      this.jumping = true;
      return this;
    };

    Jedi.prototype.setHeight = function(height) {
      this.height = height;
      return this;
    };

    var luke = new Jedi();

    luke.jump()
      .setHeight(20);
    ```


  - 可以自定义 toString() 方法，不过要确保它能运行良好，不会导致副作用

    ```javascript
    function Jedi(options) {
      options || (options = {});
      this.name = options.name || 'no name';
    }

    Jedi.prototype.getName = function getName() {
      return this.name;
    };

    Jedi.prototype.toString = function toString() {
      return 'Jedi - ' + this.getName();
    };
    ```

**[⬆ 返回顶部](#目录)**


## 事件

  - 给事件(不管是 DOM 事件还是私有的比如 Backbone 事件)绑定数据载荷时，传哈希，而不要原生值。这样的话，之后的贡献者无需查找、更新每个事件处理器就可以添加更多数据到事件载荷。比如，不要这样写：

    ```js
    // 劣
    $(this).trigger('listingUpdated', listing.id);

    ...

    $(this).on('listingUpdated', function(e, listingId) {
      // do something with listingId
    });
    ```

    而应：

    ```js
    // 优
    $(this).trigger('listingUpdated', { listingId : listing.id });

    ...

    $(this).on('listingUpdated', function(e, data) {
      // do something with data.listingId
    });
    ```

  **[⬆ 返回顶部](#目录)**


## 模块

  - 模块应该以 `!` 开始。这能保证脚本合并时，某些忘了添加最末的分号的模块在生产环境中不会出错。[解释](https://github.com/airbnb/javascript/issues/44#issuecomment-13063933)
  - 文件名应该用驼峰拼写，放在同名文件夹下，与唯一的输出名称一致
  - 加一个 `noConflict()` 方法，用于输出上一版本的模块，并且返回当前一个
  - 总是在模块顶部声明 `'use strict'

    ```javascript
    // fancyInput/fancyInput.js

    !function(global) {
      'use strict';

      var previousFancyInput = global.FancyInput;

      function FancyInput(options) {
        this.options = options || {};
      }

      FancyInput.noConflict = function noConflict() {
        global.FancyInput = previousFancyInput;
        return FancyInput;
      };

      global.FancyInput = FancyInput;
    }(this);
    ```

**[⬆ 返回顶部](#目录)**


## jQuery

  - jQuery 对象变量前加 `$`

    ```javascript
    // 劣
    var sidebar = $('.sidebar');

    // 优
    var $sidebar = $('.sidebar');
    ```

  - Cache jQuery lookups.

    ```javascript
    // 劣
    function setSidebar() {
      $('.sidebar').hide();

      // ...stuff...

      $('.sidebar').css({
        'background-color': 'pink'
      });
    }

    // 优
    function setSidebar() {
      var $sidebar = $('.sidebar');
      $sidebar.hide();

      // ...stuff...

      $sidebar.css({
        'background-color': 'pink'
      });
    }
    ```

  - DOM 查询使用层级 `$('.sidebar ul')` 或 父 > 子 `$('.sidebar > ul')`。 [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)
  - 使用 `find` 限定 jQuery 对象查找范围

    ```javascript
    // 劣
    $('ul', '.sidebar').hide();

    // 劣
    $('.sidebar').find('ul').hide();

    // 优
    $('.sidebar ul').hide();

    // 优
    $('.sidebar > ul').hide();

    // 优
    $sidebar.find('ul').hide();
    ```

**[⬆ 返回顶部](#目录)**


## ECMAScript 5 兼容性

  - 请点击 [Kangax](https://twitter.com/kangax/)'s ES5 [compatibility table](http://kangax.github.com/es5-compat-table/)

**[⬆ 返回顶部](#目录)**


## 测试

  - **乖。**

    ```javascript
    function() {
      return true;
    }
    ```

**[⬆ 返回顶部](#目录)**


## 性能

  - [On Layout & Web Performance](http://kellegous.com/j/2013/01/26/layout-performance/)
  - [String vs Array Concat](http://jsperf.com/string-vs-array-concat/2)
  - [Try/Catch Cost In a Loop](http://jsperf.com/try-catch-in-loop-cost)
  - [Bang Function](http://jsperf.com/bang-function)
  - [jQuery Find vs Context, Selector](http://jsperf.com/jquery-find-vs-context-sel/13)
  - [innerHTML vs textContent for script text](http://jsperf.com/innerhtml-vs-textcontent-for-script-text)
  - [Long String Concatenation](http://jsperf.com/ya-string-concat)
  - Loading...

**[⬆ 返回顶部](#目录)**


## 资料


**请阅读**

  - [Annotated ECMAScript 5.1](http://es5.github.com/)

**其他的书写指南**

  - [Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
  - [jQuery Core Style Guidelines](http://docs.jquery.com/JQuery_Core_Style_Guidelines)
  - [Principles of Writing Consistent, Idiomatic JavaScript](https://github.com/rwldrn/idiomatic.js/)

**其他的规范**

  - [Naming this in nested functions](https://gist.github.com/4135065) - Christian Johansen
  - [Conditional Callbacks](https://github.com/airbnb/javascript/issues/52)
  - [Popular JavaScript Coding Conventions on Github](http://sideeffect.kr/popularconvention/#javascript)
  - [Multiple var statements in JavaScript, not superfluous](http://benalman.com/news/2012/05/multiple-var-statements-javascript/) - Ben Alman

**扩展阅读**

  - [Understanding JavaScript Closures](http://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/) - Angus Croll
  - [Basic JavaScript for the impatient programmer](http://www.2ality.com/2013/06/basic-javascript.html) - Dr. Axel Rauschmayer
  - [You Might Not Need jQuery](http://youmightnotneedjquery.com/) - Zack Bloom & Adam Schwartz
  - [ES6 Features](https://github.com/lukehoban/es6features) - Luke Hoban

**书籍**

  - [JavaScript: The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742) - Douglas Crockford
  - [JavaScript Patterns](http://www.amazon.com/JavaScript-Patterns-Stoyan-Stefanov/dp/0596806752) - Stoyan Stefanov
  - [Pro JavaScript Design Patterns](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X)  - Ross Harmes and Dustin Diaz
  - [High Performance Web Sites: Essential Knowledge for Front-End Engineers](http://www.amazon.com/High-Performance-Web-Sites-Essential/dp/0596529309) - Steve Souders
  - [Maintainable JavaScript](http://www.amazon.com/Maintainable-JavaScript-Nicholas-C-Zakas/dp/1449327680) - Nicholas C. Zakas
  - [JavaScript Web Applications](http://www.amazon.com/JavaScript-Web-Applications-Alex-MacCaw/dp/144930351X) - Alex MacCaw
  - [Pro JavaScript Techniques](http://www.amazon.com/Pro-JavaScript-Techniques-John-Resig/dp/1590597273) - John Resig
  - [Smashing Node.js: JavaScript Everywhere](http://www.amazon.com/Smashing-Node-js-JavaScript-Everywhere-Magazine/dp/1119962595) - Guillermo Rauch
  - [Secrets of the JavaScript Ninja](http://www.amazon.com/Secrets-JavaScript-Ninja-John-Resig/dp/193398869X) - John Resig and Bear Bibeault
  - [Human JavaScript](http://humanjavascript.com/) - Henrik Joreteg
  - [Superhero.js](http://superherojs.com/) - Kim Joar Bekkelund, Mads Mobæk, & Olav Bjorkoy
  - [JSBooks](http://jsbooks.revolunet.com/)
  - [Third Party JavaScript](http://manning.com/vinegar/) - Ben Vinegar and Anton Kovalyov

**博客**

  - [DailyJS](http://dailyjs.com/)
  - [JavaScript Weekly](http://javascriptweekly.com/)
  - [JavaScript, JavaScript...](http://javascriptweblog.wordpress.com/)
  - [Bocoup Weblog](http://weblog.bocoup.com/)
  - [Adequately Good](http://www.adequatelygood.com/)
  - [NCZOnline](http://www.nczonline.net/)
  - [Perfection Kills](http://perfectionkills.com/)
  - [Ben Alman](http://benalman.com/)
  - [Dmitry Baranovskiy](http://dmitry.baranovskiy.com/)
  - [Dustin Diaz](http://dustindiaz.com/)
  - [nettuts](http://net.tutsplus.com/?s=javascript)

**[⬆ 返回顶部](#目录)**

## 其他

  这是一个使用这个书写指南的组织的列表。请给我们 pull request 或打开一个问题，我们会将你加到列表中。

  - **Aan Zee**: [AanZee/javascript](https://github.com/AanZee/javascript)
  - **Airbnb**: [airbnb/javascript](https://github.com/airbnb/javascript)
  - **American Insitutes for Research**: [AIRAST/javascript](https://github.com/AIRAST/javascript)
  - **Compass Learning**: [compasslearning/javascript-style-guide](https://github.com/compasslearning/javascript-style-guide)
  - **DailyMotion**: [dailymotion/javascript](https://github.com/dailymotion/javascript)
  - **Digitpaint** [digitpaint/javascript](https://github.com/digitpaint/javascript)
  - **ExactTarget**: [ExactTarget/javascript](https://github.com/ExactTarget/javascript)
  - **Gawker Media**: [gawkermedia/javascript](https://github.com/gawkermedia/javascript)
  - **GeneralElectric**: [GeneralElectric/javascript](https://github.com/GeneralElectric/javascript)
  - **GoodData**: [gooddata/gdc-js-style](https://github.com/gooddata/gdc-js-style)
  - **Grooveshark**: [grooveshark/javascript](https://github.com/grooveshark/javascript)
  - **How About We**: [howaboutwe/javascript](https://github.com/howaboutwe/javascript)
  - **Intent Media**: [intentmedia/javascript](https://github.com/intentmedia/javascript)
  - **Mighty Spring**: [mightyspring/javascript](https://github.com/mightyspring/javascript)
  - **MinnPost**: [MinnPost/javascript](https://github.com/MinnPost/javascript)
  - **ModCloth**: [modcloth/javascript](https://github.com/modcloth/javascript)
  - **Money Advice Service**: [moneyadviceservice/javascript](https://github.com/moneyadviceservice/javascript)
  - **Muber**: [muber/javascript](https://github.com/muber/javascript)
  - **National Geographic**: [natgeo/javascript](https://github.com/natgeo/javascript)
  - **National Park Service**: [nationalparkservice/javascript](https://github.com/nationalparkservice/javascript)
  - **Orion Health**: [orionhealth/javascript](https://github.com/orionhealth/javascript)
  - **Peerby**: [Peerby/javascript](https://github.com/Peerby/javascript)
  - **Razorfish**: [razorfish/javascript-style-guide](https://github.com/razorfish/javascript-style-guide)
  - **reddit**: [reddit/styleguide/javascript](https://github.com/reddit/styleguide/tree/master/javascript)
  - **REI**: [reidev/js-style-guide](https://github.com/reidev/js-style-guide)
  - **Ripple**: [ripple/javascript-style-guide](https://github.com/ripple/javascript-style-guide)
  - **SeekingAlpha**: [seekingalpha/javascript-style-guide](https://github.com/seekingalpha/javascript-style-guide)
  - **Shutterfly**: [shutterfly/javascript](https://github.com/shutterfly/javascript)
  - **TheLadders**: [TheLadders/javascript](https://github.com/TheLadders/javascript)
  - **Userify**: [userify/javascript](https://github.com/userify/javascript)
  - **Zillow**: [zillow/javascript](https://github.com/zillow/javascript)
  - **ZocDoc**: [ZocDoc/javascript](https://github.com/ZocDoc/javascript)

## 翻译

  本指南有以下语言版本的翻译：

  - :de: **德语**: [timofurrer/javascript-style-guide](https://github.com/timofurrer/javascript-style-guide)
  - :jp: **日语**: [mitsuruog/javacript-style-guide](https://github.com/mitsuruog/javacript-style-guide)
  - :br: **葡萄牙语**: [armoucar/javascript-style-guide](https://github.com/armoucar/javascript-style-guide)
  - :cn: **中文**: [adamlu/javascript-style-guide](https://github.com/adamlu/javascript-style-guide)
  - :es: **西班牙语**: [paolocarrasco/javascript-style-guide](https://github.com/paolocarrasco/javascript-style-guide)
  - :kr: **韩语**: [tipjs/javascript-style-guide](https://github.com/tipjs/javascript-style-guide)
  - :fr: **法语**: [nmussy/javascript-style-guide](https://github.com/nmussy/javascript-style-guide)
  - :ru: **俄罗斯语**: [uprock/javascript](https://github.com/uprock/javascript)
  - :bg: **保加利亚语**: [borislavvv/javascript](https://github.com/borislavvv/javascript)

## JavaScript 书写指南的指南

  - [参考](https://github.com/airbnb/javascript/wiki/The-JavaScript-Style-Guide-Guide)

## 贡献者

  - [查看贡献者](https://github.com/airbnb/javascript/graphs/contributors)


## 授权条款

(The MIT License)

Copyright (c) 2014 Airbnb

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**[⬆ 返回顶部](#目录)**

# };
