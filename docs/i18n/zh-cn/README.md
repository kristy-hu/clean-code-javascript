## JavaScript 代码整洁之道 <!-- {docsify-ignore} -->

> 作者：[@ryanmcdermott](https://github.com/ryanmcdermott/clean-code-javascript) | 译者：[@beginor](https://github.com/beginor/clean-code-javascript) | 编辑：[@kristy-hu](https://github.com/kristy-hu)

## 简介

![一张用你阅读代码时吐槽的数量来评估软件质量的搞笑图片](http://www.osnews.com/images/comics/wtfm.jpg)

将 Robert C. Martin 的 [*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882) 的代码原则适配到 JavaScript 。本文不是一篇代码风格指南，而是一个用 JavaScript 生产 [可读，可复用，以及可重构的软件](https://www.notion.so/3rs-of-software-architecture.md) [（原文）](https://github.com/ryanmcdermott/3rs-of-software-architecture)的指南。

本文所涉及的原则没必要百分百遵守，人人都能适用的原则更是没几个。这些原则不过是框架，但却是 *Clean Code* 作者多年的经验之谈。

我们软件行业只发展了短短 50 年，还有很多需要我们去学习。等到软件架构与建筑架构一样古老时，也许我们需要遵守更多硬性规则。而现在，让本文所涉及的这些原则成为你和你团队生产 JavaScript 代码的质量准则吧。

再提一句： 熟悉这些原则并不能立马让你成为一个更出色的软件开发，遵循这些原则多年也并不意味着你不再犯错。每一段代码都是从草稿开始，就像陶瓷最开始也是从粘土做起。当我们和同伴一起审代码、提高质量时，不要因为最初要改进的草稿代码而自责，要怪就怪这些代码！

## **变量**

### 使用有意义且可读的变量名称

**不推荐：**

```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');

```

**推荐：**

```javascript
const currentDat/1/2 moment().format('YYYY/MM/DD');

```

### 对同类变量使用同一名称

**不推荐：**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();

```

**推荐：**

```javascript
getUser();

```

### 使用可搜索的名称

我们要读的代码比要写的多得多，所以我们写代码的可读性和可搜索性很重要。使用无意义的变量名会导致我们的程序难以理解，也会伤害我们的读者，所以尽量使用可搜索的变量名。类似
[buddy.js](https://github.com/danielstjules/buddy.js) 和 [ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md) 的工具可以帮助我们找到未命名的常量。

**不推荐：**

```javascript
// 艹，86400000 是什么鬼？
setTimeout(blastOff, 86400000);

```

**推荐：**

```javascript
// 将它们声明为全大写的常量 `const` 。
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```

### 使用解释性变量

**不推荐：**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
	address.match(cityZipCodeRegex)[1], 
	address.match(cityZipCodeRegex)[2]
	);
```

**推荐：**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);

```

### 避免让人臆测

写清楚比藏着掖着好。

**不推荐：**

```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // 等等，`l` 是啥？
  dispatch(l);
});

```

**推荐：**

```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});

```

### 不添加不必要的上下文

如果你的类名/对象名有意义，不要在变量名上重复。

**不推荐：**

```javascript
const Car = {
  carMake: 'Honda',
  carModel: 'Accord',
  carColor: 'Blue'
};

function paintCar(car) {
  car.carColor = 'Red';
}

```

**推荐：**

```javascript
const Car = {
  make: 'Honda',
  model: 'Accord',
  color: 'Blue'
};

function paintCar(car) {
  car.color = 'Red';
}

```

### 使用默认变量替代短路或条件语句

默认参数往往比短路写法更清晰。注意，如果你使用短路，你的函数只会给未定义表达式赋予默认值。其他“不正确”的值，如`''` 、`""`、`false`、`null`、`0`和`NaN`，均不会被替换为默认值。

**不推荐：**

```javascript
function createMicrobrewery(name) {
  const breweryNam = name || 'Hipster Brew Co.';
  // ...
}

```

**推荐：**

```javascript
function createMicrobrewery(name = 'Hipster Brew Co.') {
  // ...
}

```

## **函数**

### 函数表达式 (两个或以下最理想)

限制函数的参数数量非常重要，因为它会让你的函数更容易测试。一旦超过三个参数就会难度大增，导致你不得不针对每个参数写大量测试用例。

没有参数最为理想，一个或两个也可以接受，三个参数就应避免，三个以上就应考虑重构。通常，如果你有一个大于两个参数的函数，那就意味着你要求函数做了太多事。否则，一般一个更高级的对象就能满足需求。

由于 JavaScript 允许我们不定义类型/模板就可以创建对象，当你发现你自己需要大量的参数时，你可以使用一个对象。

为了清楚说明函数需要什么属性，你可以使用 ES2015/ES6 解构语法。

有以下几个优点：

1. 当有人查看函数签名时，能立即明白正在使用哪些属性。
2. 解构可以用于模拟命名参数。
3. 解构还可以克隆传入函数的参数对象的指定原始值。这可以防止副作用。注意：从参数对象解构的对象和数组不会被克隆。
4. Linters 可以警告你未使用的属性，如果不解构，这点是做不到的。

**不推荐：**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
createMenu("Foo", "Bar", "Baz", true);
```

**推荐：**

```javascript
const menuConfig = {
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
};

function createMenu(config) {
  // ...
}

```

### 单个函数应该只做一件事情

这是软件工程中最重要的一条规则。当函数需要做更多的事情时，它们会很难写、很难测试也很难推导。 当你能将一个函数限制到只做一个动作，它们将更容易重构，且你的代码更易阅读。如果你严格遵守本指南中的这一条，你将领先于许多开发者。

**不推荐：**

```javascript
function emailClients(clients) {
  clients.forEach((client) => {
    const clientRecord = database.lookup(client);
    if (clientRecord.isActive()) {
      email(client);
    }
  });
}

```

**推荐：**

```javascript
function emailClients(clients) {
  clients
    .filter(isClientActive)
    .forEach(email);
}

function isClientActive(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}

```

### 函数名称应该说明它要做什么

**不推荐：**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// 很难从函数名看出加了什么
addToDate(date, 1);

```

**推荐：**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);

```

### 函数应该只有一个抽象级别

当在你的函数存在多个抽象级别时，你的函数通常做了太多事。拆分函数将提升重用性和测试性。

**不推荐：**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      // ...
    });
  });

  const ast = [];
  tokens.forEach((token) => {
    // lex...
  });

  ast.forEach((node) => {
    // parse...
  });
}

```

**推荐：**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach(node => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach(REGEX => {
    statements.forEach(statement => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach(token => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

### 移除冗余代码

尽力避免冗余代码。冗余代码不好，因为当你要修改逻辑时，需要同时修改多个地方。

想象一下你在经营一家餐馆，你需要记录所有库存里的西红柿、洋葱、大蒜，各种香料等等。如果你有多个列表，当你用西红柿做菜时你得更新多个列表。如果你只有一个列表，就只有一个地方需要更新！

你之所以有冗余代码，通常是因为你有两个或多个略有不同的东西：它们绝大部分相同，但是它们的不同之处迫使你用两个或更多独立的函数来处理大部分相同内容。移除冗余代码意味着创建一个可以处理这些不同之处的抽象的函数/模块/类。

弄对抽象级别是关键，这是为什么要你遵循 *Classes* 那一章的 SOLID 原因。不好的抽象级别比冗余代码更糟糕，所以要谨慎。一定要在能做出一个好的抽象级别后，才去做。不要重复造轮子，否则你会发现每当你要修改一个地方时，需要修改多处。

**不推荐：**

```javascript
function showDeveloperList(developers) {
  developers.forEach(developer => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink
    };

    render(data);
  });
}

function showManagerList(managers) {
  managers.forEach(manager => {
    const expectedSalary = manager.calculateExpectedSalary();
    const experience = manager.getExperience();
    const portfolio = manager.getMBAProjects();
    const data = {
      expectedSalary,
      experience,
      portfolio
    };

    render(data);
  });
}

```

**推荐：**

```javascript
function showEmployeeList(employees) {
  employees.forEach(employee => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case "manager":
        data.portfolio = employee.getMBAProjects();
        break;
      case "developer":
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```

### 使用 Object.assign 设置默认对象

**不推荐：**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || "Foo";
  config.body = config.body || "Bar";
  config.buttonText = config.buttonText || "Baz";
  config.cancellable =
    config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);

```

**推荐：**

```javascript
const menuConfig = {
  title: "Order",
  // User did not include 'body' key
  buttonText: "Send",
  cancellable: true
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true
    },
    config
  );
  return finalConfig
  // config now equals: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);

```

### 不要使用标记位作为函数参数

标记位是告诉你的用户这个函数做了不只一件事情。函数应该只做一件事情。如果你的函数因为一个布尔值出现了不同代码路径，请进行拆分。

**不推荐：**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}

```

**推荐：**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}

```

### 避免副作用（第一部分）

如果一个函数做了除接受一个值，然后返回一个（或多个）值外的任何事情，它将产生副作用。副作用可能是写入一个文件，修改一个全局变量，或者意外的把你所有的钱连接到一个陌生人那里。

偶尔，你的程序中确实需要副作用，以之前的代码为例，你也许需要写入到一个文件，你需要集中到一块，不要让多个函数/类写入一个特定的文件，用一个服务，用且只用一个。

重点是避免这些常见错误，比如在对象之间共享状态而不使用任何结构，使用任何地方都可以写入的可变的数据类型，没有集中化处理。如果你能做到这些，那么你将会比其它的码农大军更加幸福。

**不推荐：**

```javascript

// Global variable referenced by following function.
// 全局变量被下面的函数引用
// If we had another function that used this name, now it'd be an array and it could break it.
// 如果我们有另一个函数使用这个 name ，现在它应该是一个数组，很容易出错。

let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];

```

**推荐：**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];

```
### 避免副作用（第二部分）

在 JavaScript 中，有些值是不可以改变的（不可变的），有些值是可以改变的（可变的）。对象和数组是两种可变值，因此在将它们作为参数传入函数时，务必要小心谨慎。JavaScript 函数可以更改对象的属性或更改数组的内容，很容易导致其他地方出错。

假设有一个函数接收来自购物车的数组参数。如果这个函数对购物车数组进行更改（例如，添加购买商品），那么使用相同“购物车”数组的任何其他函数都会受到此影响。这么做有好有坏。让我们设想一个最坏的情况：用户单击“购买”按钮，按钮调用“购买”函数，“购买”函数生成网络请求并将“购物车”数组发送到服务器。由于网络连接不佳，`purchase`（购买） 函数必须不断重试请求。现在，如果在此期间用户在网络请求开始之前意外点击了他们实际上不想要的商品上的“添加到购物车”按钮，该怎么办？如果发生这种情况并且网络请求开始，则该购买函数将发送意外添加的商品，因为 `cart`（购物车） 数组已被修改。

一个很好的解决方案是让 `addItemToCart`（加到购物车） 函数始终克隆 `cart`（购物车）、编辑它并返回克隆。这将确保仍在使用旧购物车的函数不会受到更改的影响。

关于这种方法，有两点注意事项：

1. 可能存在你实际上想要修改输入对象的情况，但是当你采用这种编程方式时，你会发现这些情况非常罕见。大多数事情都可以重构而不会产生副作用！

2. 克隆大对象会严重拖累性能。幸运的是，真正实践起来问题不大，因为有
[很棒的库](https://facebook.github.io/immutable-js/) 可以让这种编程方法快速运行，而且不会像手动克隆对象和数组那样占用大量内存。

**不推荐：**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**推荐：**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

### 不要写入全局函数

污染全局在 JavaScript 中不是一个好做法，因为你可能会和另外一个类库冲突，你的 API 用户可能意识不到这一点，直到他们在生产环境得到一个异常。

举一个例子： 假设你要扩展 JavaScript 的原生 `Array` ，添加一个可以显示两个数组的不同之处的 `diff` 方法，你可以在 `Array.prototype` 中写一个新的方法，但是它可能会和尝试做相同事情的其它类库发生冲突。如果有另外一个类库仅仅使用 `diff` 方法来查找数组的第一个元素和最后一个元素之间的不同之处呢？ 这就是为什么使用 ES2015/ES6 的类是一个更好的做法的原因，只要简单的扩展全局的 `Array` 即可。

**不推荐：**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};

```

**推荐：**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}

```

### 函数式编程优于指令式编程

JavaScript 不是 Haskell 那种方式的函数式语言，但是它有函数式风格。函数式语言更加简洁且更容易进行测试，当你可以使用函数式编程风格时，请尽管使用。

**不推荐：**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}

```

**推荐：**

```javascript
const programmerOutput = [
  {
    name: "Uncle Bobby",
    linesOfCode: 500
  },
  {
    name: "Suzie Q",
    linesOfCode: 1500
  },
  {
    name: "Jimmy Gosling",
    linesOfCode: 150
  },
  {
    name: "Gracie Hopper",
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);

```

### 封装条件语句

**不推荐：**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}

```

**推荐：**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}

```

### 避免负面条件

**不推荐：**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}

```

**推荐：**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}

```

### 避免条件语句

这看起来似乎是一个不可能完成的任务。第一次听到这一原则时，多数人会说： “没有 `if` 语句还能指望我干啥呢”，答案是，多数情况下，你可以使用多态来完成同样的任务。第二个问题通常是 “好了，那么做很好，但是我为什么想要那样做呢”，答案是我们学到的上一条代码整洁之道的理念： 一个函数应当只做一件事情。 当你有使用 `if` 语句的类/函数是，你在告诉你的用户你的函数做了不止一件事情。记住： 只做一件事情。

**不推荐：**

```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case "777":
        return this.getMaxAltitude() - this.getPassengerCount();
      case "Air Force One":
        return this.getMaxAltitude();
      case "Cessna":
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}

```

**推荐：**

```javascript
class Airplane {
  // ...
}

class Boeing777 extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  // ...
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}

```

### 避免类型检查（第一部分）

JavaScript 是无类型的，换言之，你的函数能接受任何类型的参数。但有时这种自由又会反噬，让你在函数中做类型检查。来避免这情况的方法有很多，首先要考虑的是一致的 API 。

**不推荐：**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}

```

**推荐：**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}

```

### 避免类型检查（第二部分）

如果你使用原始的字符串、整数和数组，并且你不能使用多态，但是你依然感觉到有类型检查的需要，你应该考虑使用 TypeScript 。它是一个常规 JavaScript 的优秀替代品，因为它在标准的 JavaScript 语法之上为你提供静态类型。这样，既不必在常规的 JavaScript 上做人工类型检查，造成大量冗词，又不缺失可读性。保持你的 JavaScript 简洁，编写好的测试，且有好的代码审查，否则使用 TypeScript （如我所说，它是一个伟大的替代品）来完成这些。

**不推荐：**

```javascript
function combine(val1, val2) {
  if (
    (typeof val1 === "number" && typeof val2 === "number") ||
    (typeof val1 === "string" && typeof val2 === "string")
  ) {
    return val1 + val2;
  }

  throw new Error("Must be of type String or Number");
}

```

**推荐：**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}

```

### 不要过度优化

现代浏览器运行时会在幕后做大量优化。大多数的时间，做优化就是在浪费你的时间。[这些资源](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)可以用来查看哪些地方需要优化。为这些而优化，直到他们被修正。

**不推荐：**

```javascript

// On old browsers, each iteration with uncached `list.length` would be costly
// because of `list.length` recomputation. In modern browsers, this is optimized.
// 在旧的浏览器上，每次循环 `list.length` 都没有缓存，会导致不必要的重新计算。在现代化浏览器上，这个问题已被优化。
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}

```

**推荐：**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}

```

### 移除僵尸代码

僵尸代码和冗余代码同样糟糕。没有理由在代码库中保存它。如果它不会被调用，就删掉它。当你需要它时，它依然保存在版本历史记录中。

**不推荐：**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

**推荐：**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');

```

## **对象和数据结构**

### 使用 getter 和 setter

使用 getter 和 setter 来访问对象上的数据比简单的在一个对象上查找属性要好得多。“为什么？” 你可能会问，请看以下列表：

- 当你想在获取一个对象属性的背后做更多的事情时，你不需要在代码库中查找和修改每一处访问；
- 使用 `set` 可以让添加验证变得容易；
- 封装内部实现；
- 使用 getting 和 setting 时，容易添加日志和错误处理；
- 继承这个类，你可以重写默认功能；
- 你可以延迟加载对象的属性，比如说从服务器获取。

**不推荐：**

```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;

```

**推荐：**

```javascript
function makeBankAccount() {
  // this one is private
  let balance = 0;

  // a "getter", made public via the returned object below
  function getBalance() {
    return balance;
  }

  // a "setter", made public via the returned object below
  function setBalance(amount) {
    // ... validate before updating the balance
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance
  };
}

const account = makeBankAccount();
account.setBalance(100);

```

### 让对象拥有私有成员

这个可以通过闭包来实现（针对 ES5 或更低）。

**不推荐：**

```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined

```

**推荐：**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    }
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe

```

## **类**

### ES2015/ES6 类优先与 ES5 纯函数

很难为经典的 ES5 类创建可读的的继承、构造和方法定义。如果你需要继承（注意，也许你不需要），则优先用 ES2015/ES6的类。不过，短小的函数优先于类，直到你发现你需要更大并且更复杂的对象。

**不推荐：**

```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error("Instantiate Human with `new`");
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};

```

**推荐：**

```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() {
    /* ... */
  }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() {
    /* ... */
  }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() {
    /* ... */
  }
}

```

### 使用方法链

这个模式在 JavaScript 中是非常有用的，并且你可以在许多类库比如 jQuery 和 Lodash 中见到。 它使你的代码更富表现力，并减少冗余。正因为此，我说，使用方法链然后再看看你的代码会变得多么简洁。在你的类／方法中，简单的在每个方法的最后返回 `this` ，然后你就能把这个类的其它方法链在一起。

**不推荐：**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
  }

  setModel(model) {
    this.model = model;
  }

  setColor(color) {
    this.color = color;
  }

  save() {
    console.log(this.make, this.model, this.color);
  }
}

const car = new Car("Ford", "F-150", "red");
car.setColor("pink");
car.save();

```

**推荐：**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: Returning this for chaining
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: Returning this for chaining
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: Returning this for chaining
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: Returning this for chaining
    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();

```

### 组合优先于继承

正如 [*设计模式](https://en.wikipedia.org/wiki/Design_Patterns)* 所述，如果可能，你应该优先使用组合，而不是继承。使用继承的优点有很多，使用组合的优点也有很多。重点在于，如果你的本能选择是继承，那么请思考一下组合能否更好地为你的问题建模。很多情况下它真的可以。

那么你也许会这样想，“我什么时候该使用继承？” 这取决于你手上的问题，不过这儿有一个像样的列表说明什么时候继承比组合更好用：

1. 你的继承表示"is-a"的关系而不是"has-a"的关系（人类->动物 vs 用户->用户详情）；
2. 你可以重用来自基类的代码（人可以像所有动物一样行动）；
3. 你想通过基类对子类进行全局的修改（改变所有动物行动时的热量消耗）；

**不推荐：**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// Bad because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

```

**推荐：**

```javascript
class EmployeeTaxData {
  constructor(ssn, salary) {
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}

class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  setTaxData(ssn, salary) {
    this.taxData = new EmployeeTaxData(ssn, salary);
  }
  // ...
}

```

## **SOLID**

### 单一职责原则 (SRP)

正如代码整洁之道所述，“永远不要有超过一个理由来修改一个类”。给一个类塞满功能，就像你在航班上只能带一个行李箱一样，这样做的问题你的类不会有理想的内聚性，将会有太多的理由来对它进行修改。 最小化需要修改一个类的次数时很重要的，因为如果一个类拥有太多的功能，一旦你修改它的一小部分，将会很难弄清楚会对代码库中的其它模块造成什么影响。

**不推荐：**

```javascript
class UserSettings {
  constructor(user) {
    this.user = user;
  }

  changeSettings(settings) {
    if (this.verifyCredentials()) {
      // ...
    }
  }

  verifyCredentials() {
    // ...
  }
}

```

**推荐：**

```javascript
class UserAuth {
  constructor(user) {
    this.user = user;
  }

  verifyCredentials() {
    // ...
  }
}

class UserSettings {
  constructor(user) {
    this.user = user;
    this.auth = new UserAuth(user);
  }

  changeSettings(settings) {
    if (this.auth.verifyCredentials()) {
      // ...
    }
  }
}

```

### 开闭原则 (OCP)

Bertrand Meyer 说过，“软件实体 (类，模块，函数等) 应该为扩展开放，但是为修改关闭。” 这是什么意思呢？ 这个原则基本上说明了你应该允许用户添加功能而不必修改现有的代码。

**不推荐：**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === "ajaxAdapter") {
      return makeAjaxCall(url).then(response => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
      return makeHttpCall(url).then(response => {
        // transform response and return
      });
    }
  }
}

function makeAjaxCall(url) {
  // request and return promise
}

function makeHttpCall(url) {
  // request and return promise
}

```

**推荐：**

```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = "ajaxAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = "nodeAdapter";
  }

  request(url) {
    // request and return promise
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    return this.adapter.request(url).then(response => {
      // transform response and return
    });
  }
}

```

### 里氏代换原则 (LSP)

这是针对一个非常简单的概念的恐怖术语，它的正式定义是： “如果 S 是 T 的一个子类型，那么类型为 T 的对象可以被类型为 S 的对象替换（例如，类型为 S 的对象可作为类型为 T 的替代品）而不需要修改目标程序的期望性质 （正确性、任务执行性等）。” 这甚至是个恐怖的定义。

最好的解释是，如果你有一个基类和一个子类，那个基类和字类可以互换而不会产生不正确的结果。这可能还有有些疑惑，让我们来看一下这个经典的正方形与矩形的例子。从数学上说，一个正方形是一个矩形，但是你用 "is-a" 的关系用继承来实现，你将很快遇到麻烦。

**不推荐：**

```javascript
class Rectangle {
  constructor() {
    this.width = 0;
    this.height = 0;
  }

  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }

  setWidth(width) {
    this.width = width;
  }

  setHeight(height) {
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Rectangle {
  setWidth(width) {
    this.width = width;
    this.height = width;
  }

  setHeight(height) {
    this.width = height;
    this.height = height;
  }
}

function renderLargeRectangles(rectangles) {
  rectangles.forEach(rectangle => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); // BAD: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);

```

**推荐：**

```javascript
class Shape {
  setColor(color) {
    // ...
  }

  render(area) {
    // ...
  }
}

class Rectangle extends Shape {
  constructor(width, height) {
    super();
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }
}

class Square extends Shape {
  constructor(length) {
    super();
    this.length = length;
  }

  getArea() {
    return this.length * this.length;
  }
}

function renderLargeShapes(shapes) {
  shapes.forEach(shape => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);

```

### 接口隔离原则 (ISP)

JavaScript 没有接口，所以这个原则不像其它语言那么严格。不过，对于 JavaScript 这种缺少类型的语言来说，它依然重要并且有意义。

接口隔离原则说的是 “客户端不应该强制依赖他们不需要的接口。” 在 JavaScript 这种弱类型语言中，接口是隐式的契约。

在 JavaScript 中能比较好的说明这个原则的是一个类需要一个巨大的配置对象。不需要客户端去设置大量的选项是有益的，因为多数情况下他们不需要全部的设置。让它们变成可选的有助于防止出现一个“胖接口”。

**不推荐：**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.settings.animationModule.setup();
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  animationModule() {} // Most of the time, we won't need to animate when traversing.
  // ...
});

```

**推荐：**

```javascript
class DOMTraverser {
  constructor(settings) {
    this.settings = settings;
    this.options = settings.options;
    this.setup();
  }

  setup() {
    this.rootNode = this.settings.rootNode;
    this.setupOptions();
  }

  setupOptions() {
    if (this.options.animationModule) {
      // ...
    }
  }

  traverse() {
    // ...
  }
}

const $ = new DOMTraverser({
  rootNode: document.getElementsByTagName("body"),
  options: {
    animationModule() {}
  }
});

```

### 依赖反转原则 (DIP)

这个原则阐述了两个重要的事情：

1. 高级模块不应该依赖于低级模块，两者都应该依赖于抽象；
2. 抽象不应当依赖于具体实现，具体实现应当依赖于抽象。

这一原则开始可能很难理解，但是如果你使用过 Angular.js ，你应该已经看到过通过依赖注入来实现的这个原则，虽然他们不是相同的概念，依赖反转原则让高级模块远离低级模块的细节和创建，可以通过 DI 来实现。这样做的巨大益处是降低模块间的耦合。耦合是一个非常糟糕的开发模式，因为会导致代码难于重构。

如上所述，JavaScript 没有接口，所以被依赖的抽象是隐式契约。也就是说，一个对象/类的方法和属性直接暴露给另外一个对象/类。在下面的例子中，任何一个 Request 模块的隐式契约 `InventoryTracker` 将有一个 `requestItems` 方法。

**不推荐：**

```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // BAD: We have created a dependency on a specific request implementation.
    // We should just have requestItems depend on a request method: `request`
    // 不推荐： 我们已经创建了一个对请求的具体实现的依赖，我们只有一个 requestItems 方法依赖'request'
    
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();

```

**推荐：**

```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach(item => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ["HTTP"];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ["WS"];
  }

  requestItem(item) {
    // ...
  }
}

// By constructing our dependencies externally and injecting them, we can easily
// substitute our request module for a fancy new one that uses WebSockets.
// 通过外部创建依赖项并将它们注入，我们可以轻松的用一个崭新的使用 WebSockets 的请求模块进行替换。

const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();

```

## **测试**

测试比发布更重要。如果你没有测试或者测试不充分，每次发布时你就不能确认没有破坏任何东西。 测试的量由你的团队决定，但是拥有 100% 的覆盖率（包括所有语句和分支）是你为什么能达到高度自信和内心的平静。这意味着需要一个额外的伟大的测试框架，也需要一个好的[覆盖率工具](http://gotwarlost.github.io/istanbul/)。

没有理由不写测试。这里有[大量的优秀的 JS 测试框架](http://jstherightway.org/#testing-tools)，选一个适合你团队的即可。当为团队选择了测试框架之后，接下来的目标是为生产的每一个新的功能／模块编写测试。如果你倾向于测试驱动开发(TDD)，那就太棒了，但是要点是确认你在上线任何功能或者重构一个现有功能之前，达到了需要的目标覆盖率。

### 一个测试一个概念

**不推荐：**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles date boundaries", () => {
    let date;

    date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);

    date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);

    date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});

```

**推荐：**

```javascript
import assert from "assert";

describe("MomentJS", () => {
  it("handles 30-day months", () => {
    const date = new MomentJS("1/1/2015");
    date.addDays(30);
    assert.equal("1/31/2015", date);
  });

  it("handles leap year", () => {
    const date = new MomentJS("2/1/2016");
    date.addDays(28);
    assert.equal("02/29/2016", date);
  });

  it("handles non-leap year", () => {
    const date = new MomentJS("2/1/2015");
    date.addDays(28);
    assert.equal("03/01/2015", date);
  });
});

```

## **并发**

### 使用 Promise，不要使用回调

回调不够简洁，因为他们会产生过多的嵌套。在 ES2015/ES6 中，Promise 已经是内置的全局类型了，使用它们吧！

**不推荐：**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, writeErr => {
        if (writeErr) {
          console.error(writeErr);
        } else {
          console.log("File written");
        }
      });
    }
  }
);

```

**推荐：**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });

```

### Async/Await 比 Promise 更简洁

Promise 是回调的一个非常简洁的替代品，但是 ES2017/ES8 带来的 async 和 await 提供了一个更加简洁的解决方案。你需要的只是一个前缀为 `async` 关键字的函数，接下来就可以不需要 `then` 函数链来编写逻辑了。如果你能使用 ES2017/ES8 的高级功能的话，今天就使用它吧！

**不推荐：**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then(body => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch(err => {
    console.error(err);
  });

```

**推荐：**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get(
      "https://en.wikipedia.org/wiki/Robert_Cecil_Martin"
    );
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle()

```

## **错误处理**

抛出错误是一件好事！ 他们意味着当你的程序有错时运行时可以成功确认，并且通过停止执行当前堆栈上的函数来让你知道，结束当前进程（在 Node 中），在控制台中用一个堆栈跟踪提示你。

### 不要忽略捕捉到的错误

对捕捉到的错误不做任何处理不能给你修复错误或者响应错误的能力。向控制台记录错误 (`console.log`) 也不怎么好，因为往往会丢失在海量的控制台输出中。如果你把任意一段代码用 `try/catch` 包装那就意味着你想到这里可能会错，因此你应该有个修复计划，或者当错误发生时有一个代码路径。

**不推荐：**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}

```

**推荐：**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
}

```

### 不要忽略被拒绝的 promise

与你不应忽略来自 `try/catch` 的错误的原因相同。

**不推荐：**

```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  console.log(error);
});

```

**推荐：**

```javascript
getdata()
.then((data) => {
  functionThatMightThrow(data);
})
.catch((error) => {
  // One option (more noisy than console.log):
  console.error(error);
  // Another option:
  notifyUserOfError(error);
  // Another option:
  reportErrorToService(error);
  // OR do all three!
});

```

## **代码格式（美化）**

代码格式是主观的。就像其它规则一样，没有必须让你遵守的硬性规则。重点在于不要为格式起纷争，这里有[大量的工具](http://standardjs.com/rules.html)来自动格式化，使用一个即可！ 因为做为工程师去争论格式化就是在浪费时间和金钱。

针对自动格式化工具不能涵盖的问题（缩进、制表符还是空格、双引号还是单引号等），这里有一些指南。

### 使用一致的大小写

JavaScript 是无类型的，所以大小写告诉你关于你的变量、函数等的很多事情。这些规则是主观的，所以你的团队可以选择他们想要的。重点是，不管你们选择了什么，要保持一致。

**不推荐：**

```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const songs = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const Artists = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}

```

**推荐：**

```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const SONGS = ["Back In Black", "Stairway to Heaven", "Hey Jude"];
const ARTISTS = ["ACDC", "Led Zeppelin", "The Beatles"];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}

```

### 函数的调用方与被调用方应该靠近

如果一个函数调用另一个，则在代码中这两个函数的竖直位置应该靠近。理想情况下，保持被调用函数在被调用函数的正上方。我们倾向于从上到下阅读代码，就像读一章报纸。由于这个原因，保持你的代码可以按照这种方式阅读。

**不推荐：**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();

```

**推荐：**

```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  perfReview() {
    this.getPeerReviews();
    this.getManagerReview();
    this.getSelfReview();
  }

  getPeerReviews() {
    const peers = this.lookupPeers();
    // ...
  }

  lookupPeers() {
    return db.lookup(this.employee, "peers");
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, "manager");
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();

```

## **注释**

### 只对包含复杂业务逻辑的东西进行注释

注释是代码的辩解，不是要求。多数情况下，好的代码就是文档。

**不推荐：**

```javascript
function hashIt(data) {
  // The hash
  let hash = 0;

  // Length of string
  const length = data.length;

  // Loop through every character in data
  for (let i = 0; i < length; i++) {
    // Get character code.
    const char = data.charCodeAt(i);
    // Make the hash
    hash = (hash << 5) - hash + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}

```

**推荐：**

```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = (hash << 5) - hash + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```

### 不要在代码库中保存注释掉的代码

因为有版本控制，把旧的代码留在历史记录即可。

**不推荐：**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();

```

**推荐：**

```javascript
doStuff();

```

### 不要有日志式的注释

记住，使用版本控制！ 不需要僵尸代码，注释掉的代码，尤其是日志式的注释。使用 `git log` 来获取历史记录。

**不推荐：**

```javascript
/**
 * 2016-12-20: Removed monads, didn't understand them (RM)
 * 2016-10-01: Improved using special monads (JP)
 * 2016-02-03: Removed type-checking (LI)
 * 2015-03-14: Added combine with type-checking (JR)
 */
function combine(a, b) {
  return a + b;
}

```

**推荐：**

```javascript
function combine(a, b) {
  return a + b;
}

```

### 避免占位符

占位符只会平添干扰。让函数和变量名称与合适的缩进和格式为你的代码提供视觉结构。

**不推荐：**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar"
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**推荐：**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar"
};

const actions = function() {
  // ...
};

```
