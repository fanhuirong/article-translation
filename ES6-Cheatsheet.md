> 原文链接： <https://github.com/DrkSephy/es6-cheatsheet>

原文中有几年前志愿者的翻译，不过那一版已经过了四年了，我最近在看的时候就顺便自己翻了一下。

# ES6 Cheatsheet

一个包含ES2015[ES6]技巧、窍门、最佳实践和代码片段实例的小册子，适用于你的日常工作流程。欢迎大家投稿!

## 目录

- [`var`与`let/const`](#var与let/const)
- [使用代码块代替立即执行函数](#使用代码块代替立即执行函数)
- [箭头函数](#箭头函数)
- [字符串](#strings)
- [解构](#destructuring)
- [Modules](#modules)
- [参数](#parameters)
- [Classes](#classes)
- [Symbols](#symbols)
- [Maps](#maps)
- [WeakMaps](#weakmaps)
- [Promises](#promises)
- [Generators](#generators)
- [Async Await](#async-await)
- [Getter/Setter functions](#getter-and-setter-functions)
- [License](#license)

## var与let/const

> 除了`var`，我们现在可以使用两个新的标识符-`let`和`const`来存储数值。与`var` 不同的是，`let`和`const`语句不会造成变量的声明提升。

`var`的示例:

```javascript
var snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        var snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // undefined
```

观察一下下面的例子，当我们用`let`代替`var`时会发生什么？

```javascript
let snack = 'Meow Mix';

function getFood(food) {
    if (food) {
        let snack = 'Friskies';
        return snack;
    }
    return snack;
}

getFood(false); // 'Meow Mix'
```

我们在重构使用`var`相关的遗留代码时需要非常谨慎。 盲目地用`let`替换`var`的实例可能会导致预料外的结果。

> **注意**: `let`和`const`是块级作用域范围的。因此，在它们被定义之前引用块级范围内的标识符将产生一个 "引用错误" `ReferenceError`。

```javascript
console.log(x); // ReferenceError: x is not defined

let x = 'hi';
```

> **最佳实践**:  在遗留代码中留下`var`声明，以表示它需要被仔细地重构。当写新代码的时候，使用 `let`来表示能够改变其值的变量，使用`const`表示不能重新分配的常量。

<sup>[(回到目录)](#目录)</sup>

## 使用代码块代替立即执行函数

**Replacing IIFEs with Blocks**

> **立即执行函数（IIFE）** 的一个常见用法是确保变量在对应的函数范围内。ES6支持块级作用域，因此我们不再局限于函数作用域。

```javascript
(function () {
    var food = 'Meow Mix';
}());

console.log(food); // Reference Error
```

使用 `ES6` 块级作用域的版本：

```javascript
{
    let food = 'Meow Mix';
};

console.log(food); // Reference Error
```

<sup>[(回到目录)](#目录)</sup>

## 箭头函数

很多时候，我们想在嵌套函数内访问上下文的 `this`，如下面的例子：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character; // Cannot read property 'name' of undefined
    });
};
```

解决这个问题的一个常见方法是使用一个变量来保存上下文的`this`：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    var that = this; // Store the context of this
    return arr.map(function (character) {
        return that.name + character;
    });
};
```

我们也可以通过变量传入上下文的 `this`

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }, this);
};
```

还可以使用 `bind`

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(function (character) {
        return this.name + character;
    }.bind(this));
};
```

使用 **箭头函数**，`this`的值不需要被处理，我们可以重写上面的代码，如下所示：

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.prefixName = function (arr) {
    return arr.map(character => this.name + character);
};
```

> **最佳实践**: 使用**箭头函数**你无需考虑`this`的指向.

箭头函数在用于仅仅返回一个值的函数表达式时也更加简洁。

```javascript
var squares = arr.map(function (x) { return x * x }); // Function Expression
```

```javascript
const arr = [1, 2, 3, 4, 5];
const squares = arr.map(x => x * x); // Arrow Function for terser implementation
```

> **最佳实践**: 尽可能多地使用 **箭头函数** 来代替普通函数表达式

<sup>[(回到目录)](#目录)</sup>

## Strings

在ES6中，标准库得到了极大的发展。出现了一些可用于字符串的新方法，如`.includes()`和`.repeat()`。

### .includes( )

```javascript
var string = 'food';
var substring = 'foo';

console.log(string.indexOf(substring) > -1);
```

我们可以简单地使用`.includes()`返回一个布尔值来检查一个字符串的包含性，而不是检查返回值是否`>-1`。

```javascript
const string = 'food';
const substring = 'foo';

console.log(string.includes(substring)); // true
```

### .repeat( )

```javascript
function repeat(string, count) {
    var strings = [];
    while(strings.length < count) {
        strings.push(string);
    }
    return strings.join('');
}
```

在`ES6`中，我们现在可以获得更简单的实现。

```javascript
// String.repeat(numberOfRepetitions)
'meow'.repeat(3); // 'meowmeowmeow'
```

### Template Literals

使用 **模板字面量** ，我们现在可以直接构建包含特殊字符的字符串，而不需要转义它们。

```javascript
var text = "This string contains \"double quotes\" which are escaped.";
```

```javascript
let text = `This string contains "double quotes" which don't need to be escaped anymore.`;
```

**Template Literals** also support interpolation, which makes the task of
concatenating strings and values:

**模板字面量** 也支持插入变量值，这使得串联字符串和变量值的任务变得简单。

```javascript
var name = 'Tiger';
var age = 13;

console.log('My cat is named ' + name + ' and is ' + age + ' years old.');
```

更简单的版本：

```javascript
const name = 'Tiger';
const age = 13;

console.log(`My cat is named ${name} and is ${age} years old.`);
```

在ES5中，我们使用如下方式处理换行的文本：

```javascript
var text = (
    'cat\n' +
    'dog\n' +
    'nickelodeon'
);
```

或:

```javascript
var text = [
    'cat',
    'dog',
    'nickelodeon'
].join('\n');
```

**模板字面量** 中可以直接换行，而无需使用转义的换行符号：

```javascript
let text = ( `cat
dog
nickelodeon`
);
```

**模板字面量** 中可以接受表达式：

```javascript
let today = new Date();
let text = `The time and date is ${today.toLocaleString()}`;
```

<sup>[(回到目录)](#目录)</sup>

## Destructuring

解构允许我们从数组和对象（甚至是深度嵌套）中提取数值，并以更方便的语法将它们存储在变量中。

### Destructuring Arrays 解构数组

```javascript
var arr = [1, 2, 3, 4];
var a = arr[0];
var b = arr[1];
var c = arr[2];
var d = arr[3];
```

```javascript
let [a, b, c, d] = [1, 2, 3, 4];

console.log(a); // 1
console.log(b); // 2
```

### Destructuring Objects 解构对象

```javascript
var luke = { occupation: 'jedi', father: 'anakin' };
var occupation = luke.occupation; // 'jedi'
var father = luke.father; // 'anakin'
```

```javascript
let luke = { occupation: 'jedi', father: 'anakin' };
let {occupation, father} = luke;

console.log(occupation); // 'jedi'
console.log(father); // 'anakin'
```

<sup>[(回到目录)](#目录)</sup>

## Modules **TO FIX

在 ES6 之前, 我们使用诸如 [Browserify](http://browserify.org/)
之类的库在客户端建立模块，使用 [require](https://nodejs.org/api/modules.html#modules_module_require_id)在 **Node.js**中建立. 在 ES6 中, 我们现在可以直接使用AMD 和 CommonJS这些模块了。

### Exporting in CommonJS

```javascript
module.exports = 1;
module.exports = { foo: 'bar' };
module.exports = ['foo', 'bar'];
module.exports = function bar () {};
```

### Exporting in ES6

ES6 中，我们有各种不同的导出方式。我们可以使用 **变量名导出** ：

```javascript
export let name = 'David';
export let age  = 25;​​
```

还可以导出一个对象的列表：

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

export { sumTwo, sumThree };
```

我们还可以通过使用`export`关键字来导出函数、对象和值（等等）：

```javascript
export function sumTwo(a, b) {
    return a + b;
}

export function sumThree(a, b, c) {
    return a + b + c;
}
```

最后，我们可以导出 **默认导出对象** 。

```javascript
function sumTwo(a, b) {
    return a + b;
}

function sumThree(a, b, c) {
    return a + b + c;
}

let api = {
    sumTwo,
    sumThree
};

export default api;

/* Which is the same as
 * export { api as default };
 */
```

> **最佳时间**: 总是在模块的 最后 使用`export default`方法。它使人们清楚地知道被导出的内容，并节省了寻找模块导出值的时间。更多的是，CommonJS模块的常见做法是导出一个单一的值或对象。通过坚持这种规范，我们的代码会更加易于阅读，并更加容易在CommonJS和ES6模块之间进行切换。

### Importing in ES6

ES6为我们提供了各种类型的导入方式。我们可以导入整个文件：

```javascript
import 'underscore';
```

> 值得注意的是，简单地**导入整个文件将在文件顶层执行被导入文件所有代码。** ？？

与Python类似，我们可以通过模块的名称引用：

```javascript
import { sumTwo, sumThree } from 'math/addition';
```

我们也可以使用`as`重命名这些引入的模块：

```javascript
import {
    sumTwo as addTwoNumbers,
    sumThree as sumThreeNumbers
} from 'math/addition';
```

此外，我们也可以 **引入全部内容** （也叫命名空间导入）

```javascript
import * as util from 'math/addition';
```

最后，我们可以从一个模块中引入多个值：

```javascript
import * as additionUtil from 'math/addition';
const { sumTwo, sumThree } = additionUtil;
```

像这样引入默认的到处对象：

```javascript
import api from 'math/addition';
// Same as: import { default as api } from 'math/addition';
```

虽然最好保持简单的导出，但如果需要，我们有时可以混合默认引用和命名引用。当我们像这样导出时：

```javascript
// foos.js
export { foo as default, foo1, foo2 };
```

我们可以这样引用模块的值：

```javascript
import foo, { foo1, foo2 } from 'foos';
```

当引入commonjs模块，如React时：

```javascript
import React from 'react';
const { Component, PropTypes } = React;
```

更简单的版本：

```javascript
import React, { Component, PropTypes } from 'react';
```

> **注意**: 被导出的值是绑定的，而不是引用 Values that are exported are **bindings**, not references.因此，避免修改这些公共接口的导出值，因为更改一个模块中的值的绑定将影响到其他引用到这个值的地方。

<sup>[(回到目录)](#目录)</sup>

## Parameters

在ES5中，我们有不同的方法来处理那些需要**默认参数**、**不确定数量的参数**和**命名参数**的函数。在ES6中，我们可以使用更简洁的语法来完成这些。

### Default Parameters

```javascript
function addTwoNumbers(x, y) {
    x = x || 0;
    y = y || 0;
    return x + y;
}
```

在ES6中，我们可以简单地为函数参数启用默认值：

```javascript
function addTwoNumbers(x=0, y=0) {
    return x + y;
}
```

```javascript
addTwoNumbers(2, 4); // 6
addTwoNumbers(2); // 2
addTwoNumbers(); // 0
```

### Rest Parameters 剩余参数

在ES5中，我们是这样处理不确定数量的参数的：

```javascript
function logArguments() {
    for (var i=0; i < arguments.length; i++) {
        console.log(arguments[i]);
    }
}
```

使用`...`操作符，我们可以传入无限数量的参数：

```javascript
function logArguments(...args) {
    for (let arg of args) {
        console.log(arg);
    }
}
```

### Named Parameters

ES5中处理多个命名参数的方法之一是传入一个`对象`的模式，这是jQuery中采用的方法。

```javascript
function initializeCanvas(options) {
    var height = options.height || 600;
    var width  = options.width  || 400;
    var lineStroke = options.lineStroke || 'black';
}
```

我们可以使用`解构`来实现同样的功能。

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'}) {
        // Use variables height, width, lineStroke here
    }
```

如果我们想让所有参数值都是`可选的`，我们可以通过**解构一个空对象**来实现。

```javascript
function initializeCanvas(
    { height=600, width=400, lineStroke='black'} = {}) {
        // ...
    }
```

### Spread Operator 展开操作符

在ES5中，我们可以通过使用`Math.max`的`apply`方法找到数组中数值的最大值，如下所示：

```javascript
Math.max.apply(null, [-1, 100, 9001, -32]); // 9001
```

在ES6中，我们现在可以使用`展开操作符`来传递一个数组的值，把他们作为函数的参数传入。

```javascript
Math.max(...[-1, 100, 9001, -32]); // 9001
```

我们可以用这种直观的语法轻松地连接多个数组。

```javascript
let cities = ['San Francisco', 'Los Angeles'];
let places = ['Miami', ...cities, 'Chicago']; // ['Miami', 'San Francisco', 'Los Angeles', 'Chicago']
```

<sup>[(回到目录)](#目录)</sup>

## Classes

在ES6之前，我们通过创建一个构造函数来实现（implement）类，并通过扩展（extend）原型添加属性。

```javascript
function Person(name, age, gender) {
    this.name   = name;
    this.age    = age;
    this.gender = gender;
}

Person.prototype.incrementAge = function () {
    return this.age += 1;
};
```

并通过以下方式创建继承了父类属性的子类：

```javascript
function Personal(name, age, gender, occupation, hobby) {
    Person.call(this, name, age, gender);
    this.occupation = occupation;
    this.hobby = hobby;
}

Personal.prototype = Object.create(Person.prototype);
Personal.prototype.constructor = Personal;
Personal.prototype.incrementAge = function () {
    Person.prototype.incrementAge.call(this);
    this.age += 20;
    console.log(this.age);
};
```

ES6提供了一些语法糖来实现这些功能，我们可以直接创建类：

```javascript
class Person {
    constructor(name, age, gender) {
        this.name   = name;
        this.age    = age;
        this.gender = gender;
    }

    incrementAge() {
      this.age += 1;
    }
}
```

并使用**extends**关键字来实现继承父类的子类。

```javascript
class Personal extends Person {
    constructor(name, age, gender, occupation, hobby) {
        super(name, age, gender);
        this.occupation = occupation;
        this.hobby = hobby;
    }

    incrementAge() {
        super.incrementAge();
        this.age += 20;
        console.log(this.age);
    }
}
```

> **最佳实践**: 虽然在ES6中创建类的语法掩盖了实现和原型的工作原理，但对于初学者来说，这是一个很好的功能，使我们能够写出更干净的代码。

<sup>[(回到目录)](#目录)</sup>

## Symbols

`Symbols`在ES6之前就已经存在了，但现在我们有一个公共接口来直接使用它们。`Symbols`是不可变的（immutable）、唯一的（unique），可以在任何`hash`中作为键使用。

### Symbol( )

`Symbol()`的一个用例：如果你想用自己的逻辑给第三方库的对象或命名空间打补丁的时候，同时你不想和第三方的库未来的更新发生冲突。例如，如果你想给`React.Component`类添加一个方法`refreshComponent`，但又确定不了这个方法会不会在下个版本中加入，你可以这么做：

```javascript
const refreshComponent = Symbol();

React.Component.prototype[refreshComponent] = () => {
    // do something
}
```

### Symbol.for(key)

`Symbol.for(key)`将创建一个`Symbol`，它仍然是`不可改变`的和`唯一的`，但可以在全局范围内被查询。对`Symbol.for(key)`的两次相同的调用将返回相同的`Symbol`实例。

注意：这不适用于`Symbol(description)`。

```javascript
Symbol('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol('foo') // false
Symbol.for('foo') === Symbol.for('foo') // true
```

`Symbols`的一个常见用例，特别是`Symbol.for(key)`，是为了实现互操作性。这可以通过让你的代码在包含一些已知接口的第三方的对象参数上寻找一个Symbol成员来实现（This can be achieved by having your code look for a Symbol member on object arguments from third parties that contain some known interface.）。比如说：

```javascript
function reader(obj) {
    const specialRead = Symbol.for('specialRead');
    if (obj[specialRead]) {
        const reader = obj[specialRead]();
        // do something with reader
    } else {
        throw new TypeError('object cannot be read');
    }
}
```

随后在其他库中：

```javascript
const specialRead = Symbol.for('specialRead');

class SomeReadableType {
    [specialRead]() {
        const reader = createSomeReaderFrom(this);
        return reader;
    }
}
```

`Symbol`用于互操作性的一个显著例子是`Symbol.iterator`，它存在于ES6的所有`可迭代类型`中：数组、字符串、生成器等。当作为一个方法调用时，它返回一个具有迭代器接口的对象。

<sup>[(回到目录)](#目录)</sup>

## Maps

`Maps`是JavaScript中非常需要的一种数据结构。在ES6之前，我们通过对象创建**hash maps** 。

```javascript
var map = new Object();
map[key1] = 'value1';
map[key2] = 'value2';
```

然而，这并不能阻止函数被带有特定属性名称的函数被意外覆盖的情况发生。

```javascript
> getOwnProperty({ hasOwnProperty: 'Hah, overwritten'}, 'Pwned');
> TypeError: Property 'hasOwnProperty' is not a function
```

实际 **Maps** 允许我们`set`, `get` 和 `search`（以及更多）。

```javascript
let map = new Map();
> map.set('name', 'david');
> map.get('name'); // david
> map.has('name'); // true
```

`Maps`最神奇的地方在于我们不再局限于只使用字符串作为 `key`。我们现在可以使用任何类型的键，而且它不会被类型转换为字符串。

```javascript
let map = new Map([
    ['name', 'david'],
    [true, 'false'],
    [1, 'one'],
    [{}, 'object'],
    [function () {}, 'function']
]);

for (let key of map.keys()) {
    console.log(typeof key);
    // > string, boolean, number, object, function
}
```

>**提示**：当使用 `map.get()` 判断值是否相等时，非基础类型比如一个函数或者对象，将不会正常工作。 有鉴于此，还是建议使用字符串，布尔和数字类型的数据类型作为`key`。

我们还可以用`. entries()`来迭代 `maps`

```javascript
for (let [key, value] of map.entries()) {
    console.log(key, value);
}
```

<sup>[(回到目录)](#目录)</sup>

## WeakMaps

在ES6之前的版本，为了存储**私有数据**，有好几种方法，其中一个是使用这种下划线命名约定：

```javascript
class Person {
    constructor(age) {
        this._age = age;
    }

    _incrementAge() {
        this._age += 1;
    }
}
```

使用下划线命名可能会在代码库中造成混乱，而且不一定会被遵守。相反，我们可以使用`WeakMaps`来存储我们的私有值。

```javascript
let _age = new WeakMap();
class Person {
    constructor(age) {
        _age.set(this, age);
    }

    incrementAge() {
        let age = _age.get(this) + 1;
        _age.set(this, age);
        if (age > 50) {
            console.log('Midlife crisis');
        }
    }
}
```

使用WeakMaps来保存我们私有数据的理由之一是不会暴露出属性名，就像下面的例子中的 `Reflect.ownKeys()`

```javascript
> const person = new Person(50);
> person.incrementAge(); // 'Midlife crisis'
> Reflect.ownKeys(person); // []
```

使用`WeakMaps`的一个更实际的例子是存储与DOM元素相关的数据，而不需要污染DOM本身。

```javascript
let map = new WeakMap();
let el  = document.getElementById('someElement');

// Store a weak reference to the element with a key
map.set(el, 'reference');

// Access the value of the element
let value = map.get(el); // 'reference'

// Remove the reference
el.parentNode.removeChild(el);
el = null;

// map is empty, since the element is destroyed
```

如上所示，一旦该对象被垃圾收集器销毁，`WeakMap`将自动删除该对象的键值对。

> **注意**：为了进一步说明这个例子的用处，请考虑jQuery是如何缓存存储一个带有引用的DOM元素。使用WeakMaps，一旦被缓存的元素从文档中删除，jQuery可以自动释放任何相关的内存。在一般情况下，如果库的内容涉及DOM元素的缓存，使用WeakMaps是非常有用的。

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Promises

Promises allow us to turn our horizontal code (callback hell):

```javascript
func1(function (value1) {
    func2(value1, function (value2) {
        func3(value2, function (value3) {
            func4(value3, function (value4) {
                func5(value4, function (value5) {
                    // Do something with value 5
                });
            });
        });
    });
});
```

Into vertical code:

```javascript
func1(value1)
    .then(func2)
    .then(func3)
    .then(func4)
    .then(func5, value5 => {
        // Do something with value 5
    });
```

Prior to ES6, we used [bluebird](https://github.com/petkaantonov/bluebird) or
[Q](https://github.com/kriskowal/q). Now we have Promises natively:

```javascript
new Promise((resolve, reject) =>
    reject(new Error('Failed to fulfill Promise')))
        .catch(reason => console.log(reason));
```

Where we have two handlers, **resolve** (a function called when the Promise is
**fulfilled**) and **reject** (a function called when the Promise is **rejected**).

> **Benefits of Promises**: Error Handling using a bunch of nested callbacks
can get chaotic. Using Promises, we have a clear path to bubbling errors up
and handling them appropriately. Moreover, the value of a Promise after it has
been resolved/rejected is immutable - it will never change.

Here is a practical example of using Promises:

```javascript
var request = require('request');

return new Promise((resolve, reject) => {
  request.get(url, (error, response, body) => {
    if (body) {
        resolve(JSON.parse(body));
      } else {
        resolve({});
      }
  });
});
```

We can also **parallelize** Promises to handle an array of asynchronous
operations by using `Promise.all()`:

```javascript
let urls = [
  '/api/commits',
  '/api/issues/opened',
  '/api/issues/assigned',
  '/api/issues/completed',
  '/api/issues/comments',
  '/api/pullrequests'
];

let promises = urls.map((url) => {
  return new Promise((resolve, reject) => {
    $.ajax({ url: url })
      .done((data) => {
        resolve(data);
      });
  });
});

Promise.all(promises)
  .then((results) => {
    // Do something with results of all our promises
 });
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Generators

Similar to how [Promises](https://github.com/DrkSephy/es6-cheatsheet#promises) allow us to avoid
[callback hell](http://callbackhell.com/), Generators allow us to flatten our code - giving our
asynchronous code a synchronous feel. Generators are essentially functions which we can
[pause their execution](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/yield)
and subsequently return the value of an expression.

A simple example of using generators is shown below:

```javascript
function* sillyGenerator() {
    yield 1;
    yield 2;
    yield 3;
    yield 4;
}

var generator = sillyGenerator();
> console.log(generator.next()); // { value: 1, done: false }
> console.log(generator.next()); // { value: 2, done: false }
> console.log(generator.next()); // { value: 3, done: false }
> console.log(generator.next()); // { value: 4, done: false }
```

Where [next](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator/next)
will allow us to push our generator forward and evaluate a new expression. While the above example is extremely
contrived, we can utilize Generators to write asynchronous code in a synchronous manner:

```javascript
// Hiding asynchronousity with Generators

function request(url) {
    getJSON(url, function(response) {
        generator.next(response);
    });
}
```

And here we write a generator function that will return our data:

```javascript
function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
}
```

By the power of `yield`, we are guaranteed that `entry1` will have the data needed to be parsed and stored
in `data1`.

While generators allow us to write asynchronous code in a synchronous manner, there is no clear
and easy path for error propagation. As such, as we can augment our generator with Promises:

```javascript
function request(url) {
    return new Promise((resolve, reject) => {
        getJSON(url, resolve);
    });
}
```

And we write a function which will step through our generator using `next` which in turn will utilize our
`request` method above to yield a Promise:

```javascript
function iterateGenerator(gen) {
    var generator = gen();
    (function iterate(val) {
        var ret = generator.next();
        if(!ret.done) {
            ret.value.then(iterate);
        }
    })();
}
```

By augmenting our Generator with Promises, we have a clear way of propagating errors through the use of our
Promise `.catch` and `reject`. To use our newly augmented Generator, it is as simple as before:

```javascript
iterateGenerator(function* getData() {
    var entry1 = yield request('http://some_api/item1');
    var data1  = JSON.parse(entry1);
    var entry2 = yield request('http://some_api/item2');
    var data2  = JSON.parse(entry2);
});
```

We were able to reuse our implementation to use our Generator as before, which shows their power. While Generators
and Promises allow us to write asynchronous code in a synchronous manner while retaining the ability to propagate
errors in a nice way, we can actually begin to utilize a simpler construction that provides the same benefits:
[async-await](https://github.com/DrkSephy/es6-cheatsheet#async-await).

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Async Await

While this is actually an upcoming ES2016 feature, `async await` allows us to perform the same thing we accomplished
using Generators and Promises with less effort:

```javascript
var request = require('request');

function getJSON(url) {
  return new Promise(function(resolve, reject) {
    request(url, function(error, response, body) {
      resolve(body);
    });
  });
}

async function main() {
  var data = await getJSON();
  console.log(data); // NOT undefined!
}

main();
```

Under the hood, it performs similarly to Generators. I highly recommend using them over Generators + Promises. A great resource
for getting up and running with ES7 and Babel can be found [here](http://masnun.com/2015/11/11/using-es7-asyncawait-today-with-babel.html).

<sup>[(back to table of contents)](#table-of-contents)</sup>

## Getter and setter functions

ES6 has started supporting getter and setter functions within classes. Using the following example:

```javascript
class Employee {

    constructor(name) {
        this._name = name;
    }

    get name() {
      if(this._name) {
        return 'Mr. ' + this._name.toUpperCase();  
      } else {
        return undefined;
      }  
    }

    set name(newName) {
      if (newName == this._name) {
        console.log('I already have this name.');
      } else if (newName) {
        this._name = newName;
      } else {
        return false;
      }
    }
}

var emp = new Employee("James Bond");

// uses the get method in the background
if (emp.name) {
  console.log(emp.name);  // Mr. JAMES BOND
}

// uses the setter in the background
emp.name = "Bond 007";
console.log(emp.name);  // Mr. BOND 007  
```

Latest browsers are also supporting getter/setter functions in Objects and we can use them for computed properties, adding listeners and preprocessing before setting/getting:

```javascript
var person = {
  firstName: 'James',
  lastName: 'Bond',
  get fullName() {
      console.log('Getting FullName');
      return this.firstName + ' ' + this.lastName;
  },
  set fullName (name) {
      console.log('Setting FullName');
      var words = name.toString().split(' ');
      this.firstName = words[0] || '';
      this.lastName = words[1] || '';
  }
}

person.fullName; // James Bond
person.fullName = 'Bond 007';
person.fullName; // Bond 007
```

<sup>[(back to table of contents)](#table-of-contents)</sup>

## License

The MIT License (MIT)

Copyright (c) 2015 David Leonard

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

<sup>[(back to table of contents)](#table-of-contents)</sup>
