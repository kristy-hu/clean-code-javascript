## Clean Code JavaScript <!-- {docsify-ignore} -->

> author: [@ryanmcdermott](https://github.com/ryanmcdermott/clean-code-javascript) | translator: [@mitsuruog](https://github.com/mitsuruog/clean-code-javascript/)

## はじめに
![Humorous image of software quality estimation as a count of how many expletives
you shout when reading code](http://www.osnews.com/images/comics/wtfm.jpg)

Robert C. Martinの本[*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)、ソフトウェアエンジニアリングの原則をJavaScriptに適用させたもの。
これはスタイルガイドではありません。JavaScriptで[可読性が良く、再利用でき、リファクタリング可能](/3rs-of-software-architecture.md) [(source)](https://github.com/ryanmcdermott/3rs-of-software-architecture)なソフトウェアを提供するためのガイドです。

すべての原則に厳密に厳格に従う必要はありません、さらに普遍的に合意されているものはさらに少くなります。
これらはガイドライン以上の何ものでもありませんが、*Clean Code* の著者達による長年の経験を集めて文書化したものの一つです。

ソフトウェアエンジニアリング技術は50歳を少し超えただけであり、いまだ多くのことを私たちは学び続けています。
もし、ソフトウェアアーキテクチャがそのアーキテクチャと同じくらい古いものであれば、おそらくそれに従うためのより厳格なルールがあることでしょう。
今のところ、これらのガイドラインはあなたとチームが提供するJavaScriptコードの品質を確認するための試金石として役立つでしょう。

もう一つ大事なこと:  
これらのことを知っているからといっても、すぐにより良い開発者にしてくれる訳ではありません。
また、長年これにしたがって作業していたとしても、間違いを犯さない訳でもありません。
全てのコードのかけらは、湿った粘土をこねて最終形を目指すように、最初ドラフトとして始まります。
最終的に同僚とこれをレビューする時に、この不完全な部分を取り除いていきます。
どうか改善が必要な最初のドラフトで自分自身を叩かないでください。代わりにコードを叩きましょう！

## **Variables**
### 意味があり発音可能な変数名を利用すること

**Bad:**
```javascript
const yyyymmdstr = moment().format('YYYY/MM/DD');
```

**Good:**
```javascript
const currentDate = moment().format('YYYY/MM/DD');
```


### 同じタイプの変数には同じ単語を利用すること

**Bad:**
```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**Good:**
```javascript
getUser();
```


### 検索できる名前を利用すること

私たちはコードを書くよりも読む方が多いでしょう。そのため、コードを読みやすく検索できるように書くことは重要なことです。
プログラムを理解するために有意義な名前を付けない変数によって、私たちは読み手を傷つけています。
変数を検索可能にしておいてください。[buddy.js](https://github.com/danielstjules/buddy.js)や
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)のようなツールは、名前が付いていない変数を識別する手助けをしてくれます。

**Bad:**
```javascript
// 一体、なんのための86400000なんだい？
setTimeout(blastOff, 86400000);

```

**Good:**
```javascript
// それらを大文字の名前付き定数として宣言してください。
const MILLISECONDS_IN_A_DAY = 86400000;

setTimeout(blastOff, MILLISECONDS_IN_A_DAY);

```

### 説明的な変数を利用すること

**Bad:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(address.match(cityZipCodeRegex)[1], address.match(cityZipCodeRegex)[2]);
```

**Good:**
```javascript
const address = 'One Infinite Loop, Cupertino 95014';
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```


### メンタルマップを避ける
明らかなことは暗黙的なことよりも優れています。

> 訳注：メンタルマップとは、認知心理学において記憶の中に構成される「あるべき姿」のイメージをさす言葉です。

**Bad:**
```javascript
const locations = ['Austin', 'New York', 'San Francisco'];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // ちょっと待って、もう一度`l`ってなんだっけ？
  dispatch(l);
});
```

**Good:**
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


### 不必要なコンテキストを加えない
もしクラスやオブジェクト名が何かを伝えているのであれば、変数名でそのことを繰り返してはいけません。

**Bad:**
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

**Good:**
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


### 短絡評価や条件の代わりにデフォルト引数を利用すること

デフォルト引数は多くの場合、短絡評価よりも明確です。
ご存知の通り、これらを使った場合、関数は`undefined`の引数のみにデフォルト値を提供します。
他の`''`、`""`、`false`、`null`、`0`や`NaN`のような"falsy"値は、デフォルト値で置き換わることはありません。

**Bad:**
```javascript
function createMicrobrewery(name) {
  const breweryName = name || 'Hipster Brew Co.';
  // ...
}

```

**Good:**
```javascript
function createMicrobrewery(name = 'Hipster Brew Co.') {
  // ...
}

```


## **Functions**
### 関数の引数(2つ以下が理想的)

関数の引数の数を制限することは、テストを簡単に行えるという点において非常に重要なことです。
3つ以上あるということは、それぞれ別の引数を伴った数多くの異なるケースをテストしなければならないという、組み合わせ爆発につながります。

1つか2つの場合は理想的で、3つは可能であれば避けるべきです。
それ以上のものは統合する必要があります。通常2つ以上の引数を持つ場合、その関数は余りにも多くのことをやろうとしています。
そうでない場合、ほとんどの場合、上位のオブジェクトを引数とすれば十分でしょう。

JavaScriptは、多くのクラスの雛形がなくとも素早くオブジェクトを作成することができるため、もし多くの引数を必要としているとわかった場合は、オブジェクトを使うことができます。

関数がどのプロパティを期待しているかを明らかにするために、ES2015/ES6の分割代入(destructuring)構文を利用することができます。
これにはいくつかの利点があります。

1. 誰かが関数の定義を見たときに、どのプロパティが利用されているかが明らかです。
2. 分割代入は、関数の中に渡された引数オブジェクトの指定されたプリミティブ型の値を複製します。これは副作用を防ぐ役割をします。注意:引数オブジェクトから再構成されたオブジェクトや配列は複製されません。
3. Lintツールが、未使用のプロパティについて警告を出すことができます。このようなことは、分割代入しない限り不可能でしょう。

**Bad:**
```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}
```

**Good:**
```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: 'Foo',
  body: 'Bar',
  buttonText: 'Baz',
  cancellable: true
});
```

### 関数は1つのことを行うこと

これはいままでのところ、ソフトウェアエンジニアリングにとってもっとも重要なルールです。
関数が2つ以上のことをやるときは、それを作ったり、テストしたり、理由付けすることが難しくなります。
関数をただ1つのことをやるように分離できた場合、それらを簡単にリファクタリングしたり、コードをかなりしっかりと読むことができます。
このガイドのこれ以外のことをなにもしなかったとしても、これをすることだけで、あなたは他の開発者よりも少し先に進んでいると言えます。

**Bad:**
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

**Good:**
```javascript
function emailActiveClients(clients) {
  clients
    .filter(isActiveClient)
    .forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```


### 関数名は何をするかを表すこと

**Bad:**
```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// 関数名からは何が追加されたのかがわかりにくい
addToDate(date, 1);
```

**Good:**
```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```


### 関数はただ1つの抽象化をすること

関数が1つ以上の抽象化を行なっている場合、通常その関数は多くのことをやり過ぎています。関数を分割することで、再利用やテストが簡単になります。

**Bad:**
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

**Good:**
```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const ast = lexer(tokens);
  ast.forEach((node) => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(' ');
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push( /* ... */ );
    });
  });

  return tokens;
}

function lexer(tokens) {
  const ast = [];
  tokens.forEach((token) => {
    ast.push( /* ... */ );
  });

  return ast;
}
```


### 重複したコードを削除すること

重複したコードを避けるために絶対にベストを尽くしてください。
重複したコードは、もし何かのロジックを変更しようとした場合、何か変更する場所が1つ以上あるという意味で悪です。

あなたがレストランを経営していて、すべてのトマト、たまねぎ、ニンニク、スパイスなどの在庫を追跡しているとします。
もし複数のリストを持っている場合、トマトが入った料理を提供したら、全てを更新しなければなりません。
もしそれが1つだった場合、更新する場所はたった1つです！

しばしば、共有点が多いにも関わらず、2つ以上のわずかに異なる部分があるために、重複したコードを持つことがあります。
しかしその違いによって、ほとんど同じことを行う2つ以上の別々の関数が必要になります。
重複したコードを削除するということは、関数/モジュール/クラスを1つだけ利用して、これらのわずかにを異なる一連のものを処理することができる抽象化を作るということを意味します。

抽象化を正しく行うことが重要です。そのため、*クラス* セクションで説明されているSOLIDの原則に従う必要があります。
悪い抽象化は、重複コードより悪い可能性があります、注意深くしましょう！
少し難しいことではありますが、もし良い抽象化ができるのであればそれをやってください！
自分自身を繰り返さないこと。そうしなければ、1つの場所を変更したいときはいつでも、複数の場所を変更することになります。

**Bad:**
```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
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
  managers.forEach((manager) => {
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

**Good:**
```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience
    };

    switch (employee.type) {
      case 'manager':
        data.portfolio = employee.getMBAProjects();
        break;
      case 'developer':
        data.githubLink = employee.getGithubLink();
        break;
    }

    render(data);
  });
}
```


### Object.assignでデフォルトオブジェクトを設定すること

**Bad:**
```javascript
const menuConfig = {
  title: null,
  body: 'Bar',
  buttonText: null,
  cancellable: true
};

function createMenu(config) {
  config.title = config.title || 'Foo';
  config.body = config.body || 'Bar';
  config.buttonText = config.buttonText || 'Baz';
  config.cancellable = config.cancellable !== undefined ? config.cancellable : true;
}

createMenu(menuConfig);
```

**Good:**
```javascript
const menuConfig = {
  title: 'Order',
  // ユーザーは `body` キーを含めなくていい
  buttonText: 'Send',
  cancellable: true
};

function createMenu(config) {
  config = Object.assign({
    title: 'Foo',
    body: 'Bar',
    buttonText: 'Baz',
    cancellable: true
  }, config);

  // configはこれと同じになります
  // ...
}

createMenu(menuConfig);
```



### フラグを関数の引数のように利用しない

フラグは、この関数が複数のことを行うことを利用者に伝えます。
関数は1つのことを行うべきです。関数が真偽値によって異なるコードの経路を経由する場合、その関数を分割してください。

**Bad:**
```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**Good:**
```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```


### 副作用を避ける (part 1)

関数が、値を受け取り何か他の値を返す以外のことを行う場合、副作用を引き起こします。
副作用とは、ファイルを書き込みしたり、なにかのグローバル変数を書き換えたり、誤ってあなたの全てのお金を見知らぬ人に振込みするようなものです。

時には、副作用を持つプログラムを必要とします。少し前に例で挙げた、ファイルに書き込みしなければならない場合のように。
あなたがしたいことは、どこでこれを行うかを集中させることです。
ファイルを部分的に書き換えするような、関数やクラスをいくつも持たないでください。
それを行う1つのサービスを持ってください。1つ、1だけです。


重要なポイントは、何でも書き込むことができる可変長のデータ型を用いて、何の構造も無しにオブジェクト間で状態を共有し、
副作用が発生する場所を集中させないといった、共通の落とし穴を避けることです。
これを行うことができれば、大多数の他のプログラマーよりも幸せになれます。

**Bad:**
```javascript
// グローバル変数があとの関数から参照されている
// もし、この名前を別の関数で（直接）使っている場合、これは配列となって、そして壊れる。
let name = 'Ryan McDermott';

function splitIntoFirstAndLastName() {
  name = name.split(' ');
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**Good:**
```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(' ');
}

const name = 'Ryan McDermott';
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```


### 副作用を避ける (part 2)

JavaScriptでは、プリミティブ型は値渡しであり、オブジェクトと配列は参照渡しです。
オブジェクトと配列の場合、もし関数がショッピングカート内の配列に変更を加える場合、（例えば、購入するために商品を加えるなど）
この`cart`と同じ配列を使っている他の関数は、この追加の影響を受けます。

ユーザーが購入ボタンをクリックすると、`purchase`関数を呼び出し、その関数はネットワークリクエストを生成して、その`cart`配列をサーバーへ送信します。
しかし、ネットワーク接続が悪いために、`purchase`関数はリクエストを繰り返し送信し続けなければならない。

ユーザーが、ネットワークリクエストが始まる前に欲しいとは思っていない商品の"カートに追加する"ボタンをうっかりクリックしてしまった場合。
もしそれが起こって、ネットワークリクエストが始まった場合、その時、そのpurchase関数は間違って追加された商品を送信してしまいます。
なぜなら、その関数は`addItemToCart`によって望んでいない商品を追加され、変更されてしまったショッピングカート配列を参照しているからです。

良い解決策は、`addItemToCart`が常に`cart`を複製して変更し、その変更したものを返すことでしょう。
このことは、ショッピングカートへの参照を保持している他の関数は、いかなる変更の影響も受けないことを保証します。

このアプローチに関する2つの注意点:

  1. 時に、渡されたオブジェクトを変更したい場所があるケースがありますが、このプログラミング手法を採用している場合、このケースは稀だということに気づくでしょう。
  そしてほとんどの場合、副作用がないようにリファクタリングすることができます。

  1. 巨大なオブジェクトを複製することは、パフォーマンスの面で非常にコストが高いことになります。
  幸運なことに、これはこの手法においては大きな問題ではありません。なせなら、このようなプログラミングアプローチをより高速かつ、手作業でオブジェクトや配列を複製するよりもメモリ使用量を抑えることができる[素晴らしいライブラリ](https://facebook.github.io/immutable-js/)が存在するからです。

**Bad:**
```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**Good:**
```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date : Date.now() }];
};
```



### グローバル関数に書き込まない

グローバルを汚染することはJavaScriptにおけるバッドプラクティスです。
なぜなら、他のライブラリをクラッシュさせるかもしれないし、あなたのAPIを使っているユーザーは、プロダクション環境で例外を受け取るまで、そのことについて何もわからないからです。
例を考えてみましょう。もし、JavaScriptの既存のArray関数を拡張して、`diff`という2つの配列間の差分をみることができる関数を追加したいとしたらどうでしょうか？
`Array.prototype`に新しい関数を作成することができるかもしれないですが、他のライブラリで同じことをやろうとしているものをクラッシュさせるかもしれません。
もし、他のライブラリが、`diff`を単に配列の最初と最後の差分を見つけるために利用していたとしたらどうでしょう？
このことは、なぜグローバルの`Array`を単純に拡張するよりも、ES2015/ES6のクラスを使った方がより良いかという理由です。

**Bad:**
```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter(elem => !hash.has(elem));
};
```

**Good:**
```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter(elem => !hash.has(elem));
  }
}
```


### 手続き型プログラミングより関数型プログラミングを優先する

JavaScriptは、Haskellがやっているような関数型言語ではありませんが、部分的にその機能があります。
関数型言語はよりクリーンでテストしやすいものになります。あなたができる時は、このプログラミングスタイルを優先してください。

**Bad:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**Good:**
```javascript
const programmerOutput = [
  {
    name: 'Uncle Bobby',
    linesOfCode: 500
  }, {
    name: 'Suzie Q',
    linesOfCode: 1500
  }, {
    name: 'Jimmy Gosling',
    linesOfCode: 150
  }, {
    name: 'Gracie Hopper',
    linesOfCode: 1000
  }
];

const totalOutput = programmerOutput
  .map(output => output.linesOfCode)
  .reduce((totalLines, lines) => totalLines + lines);
```



### 条件をカプセル化する

**Bad:**
```javascript
if (fsm.state === 'fetching' && isEmpty(listNode)) {
  // ...
}
```

**Good:**
```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === 'fetching' && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```


### 否定的な条件を避ける

**Bad:**
```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**Good:**
```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```


### 条件文を避ける

これは一見不可能なタスクのように見えます。
最初にこれを聞いて、ほとんどの人はこう言います。「`if`文なしで、何をするの？」
この答えは、多くの場合、同じタスクを実行するためにポリモーフィズム(多様性)を使ってできるよ。ということです。
2つめの質問は大抵これです。「うーん、いいと思うんだけど、なぜそれをやりたいんだろう。。。」
この答えは、私たちが先に学んだクリーンなコードコンセプト、「関数はただ1つのことを行うべき」です。
あなたのクラスや関数が`if`文を持っているとき、この関数は1つ以上のことを行なっていることを示唆しています。
たった1つのことをやるということを覚えておいてください。

**Bad:**
```javascript
class Airplane {
  // ...
  getCruisingAltitude() {
    switch (this.type) {
      case '777':
        return this.getMaxAltitude() - this.getPassengerCount();
      case 'Air Force One':
        return this.getMaxAltitude();
      case 'Cessna':
        return this.getMaxAltitude() - this.getFuelExpenditure();
    }
  }
}
```

**Good:**
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


### 型チェックを避ける (part 1)

JavaScriptには型がありません、このことは、関数がどんな型の引数でも受け取ることができることを意味します。
ときにはこの自由に夢中になって、関数の中でタイプチェックをするような誘惑に駆られるようになります。
これをやらなければならないときに、これを避ける方法はたくさんあります。まず、最初に考えるべきことは、一貫性のあるAPIです。

**Bad:**
```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location('texas'));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location('texas'));
  }
}
```

**Good:**
```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location('texas'));
}
```


### 型チェックを避ける (part 2)

もし、あなたが文字列、数値のような基本的なプリミティブな値を扱っていて、ポリモーフィズムを使えない、しかしまだタイプチェックが必要だと感じている場合。
TypeScriptの利用を検討してみてください。
これは標準のJavaScript構文の上に静的な型を提供するので、通常のJavaScriptに変わる優れた代替品です。
通常のJavaScript手作業のタイプチェックの問題は、偽物の型安全を得るために、あまりにも多くの余計な構文が必要なことです。これらは失った可読性を補うようなものではありません。
JavaScriptをクリーンに保ち、良いテストを書き。良いコードレビューを行なってください。
それ以外の場合は、それらの全てをTypeScriptで行います。(私が言ったように、それは素晴らしい代替え品です！)

**Bad:**
```javascript
function combine(val1, val2) {
  if (typeof val1 === 'number' && typeof val2 === 'number' ||
      typeof val1 === 'string' && typeof val2 === 'string') {
    return val1 + val2;
  }

  throw new Error('Must be of type String or Number');
}
```

**Good:**
```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```


### 行き過ぎた最適化をしない

モダンブラウザは、ランタイムの中で多くの最適化を行います。
何度も最適化を行なっているのであれば、それは時間の無駄です。[ここにどこで最適化が不足するかをみるための良い資料](https://github.com/petkaantonov/bluebird/wiki/Optimization-killers)があります。
可能であれば、それらが修正されるまでは、それらだけを最適化の対象としてください。

**Bad:**
```javascript

// 古いブラウザにおいては、キャッシュされていない`list.length`はコストが掛かる
// なぜなら、`list.length`が再計算されるから。しかし、モダンプラウザでは最適化されている
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**Good:**
```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```


### 使っていないコードを削除する

使っていないコードは重複したコードと同じくらい悪いだけのものです。コードベースに残しておく理由はなにもありません。
もし、呼び出されていないのであれば、それらを取り除きましょう！
もし、まだ必要であってもバージョン管理の中にあるだけで安全でしょう。

**Bad:**
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

**Good:**
```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker('apples', req, 'www.inventory-awesome.io');
```


## **オブジェクトとデータ構造**

### gettersとsettersを使うこと

gettersとsettersを使ってオブジェクト上のデータにアクセスする方が、単純にオブジェクトのプロパティをみるよりも良くなります。
「なぜ？」と尋ねるかもしれない。これはあまりまとまっていないものですが、その理由のリストです。

* もし、オブジェクトのプロパティを取得する以上のことをやろうとした場合、コードベースの全てのアクセッサを検索して変更して回る必要がない。
* 単純に`set`を行う時に、バリデーションを追加することができる。
* 内部をカプセル化する。
* 取得や設定の時に、簡単にロギングやエラーハンドリングを追加できる。
* オブジェクトのプロパティを遅延評価することができる、これをその値をサーバーから取得すると言おう。

**Bad:**
```javascript
function makeBankAccount() {
  // ...

  return {
    balance: 0,
    // ...
  };
}

const account = makeBankAccount();
account.balance = 100;
```

**Good:**
```javascript
function makeBankAccount() {
  // これはprivate
  let balance = 0;

  // getter、以下でオブジェクトを返すことでpublicにします
  function getBalance() {
    return balance;
  }

  // setter、パラメータオブジェクト受け取ることでpublicにします(なんか原文が違う。。。)
  function setBalance(amount) {
    // ... balanceを更新するまえにバリデーションを行う
    balance = amount;
  }

  return {
    // ...
    getBalance,
    setBalance,
  };
}

const account = makeBankAccount();
account.setBalance(100);
```



### オブジェクトはプライベートなメンバーを持つようにする

これはグロージャによって達成することができます。（ES5以前の場合）

**Bad:**
```javascript

const Employee = function(name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: undefined
```

**Good:**
```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee('John Doe');
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
delete employee.name;
console.log(`Employee name: ${employee.getName()}`); // Employee name: John Doe
```



## **Classes**
### ES5の関数よりもES2015/ES6のクラスの方を好むこと

古典的なES5クラスでは、可読性の良いクラス継承、構築、メソッド定義を行うことが難しいです。
もし、継承が必要な場合（そう認識していないかもしれない）、ES2015/ES6クラスを優先してください。
しかしならがら、巨大で複雑なオブジェクトが必要だとわかるまでは、クラスよりも小さな関数を優先してください。

**Bad:**
```javascript
const Animal = function(age) {
  if (!(this instanceof Animal)) {
    throw new Error('Instantiate Animal with `new`');
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function(age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error('Instantiate Mammal with `new`');
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function(age, furColor, languageSpoken) {
  if (!(this instanceof Human)) {
    throw new Error('Instantiate Human with `new`');
  }

  Mammal.call(this, age, furColor);
  this.languageSpoken = languageSpoken;
};

Human.prototype = Object.create(Mammal.prototype);
Human.prototype.constructor = Human;
Human.prototype.speak = function speak() {};
```

**Good:**
```javascript
class Animal {
  constructor(age) {
    this.age = age;
  }

  move() { /* ... */ }
}

class Mammal extends Animal {
  constructor(age, furColor) {
    super(age);
    this.furColor = furColor;
  }

  liveBirth() { /* ... */ }
}

class Human extends Mammal {
  constructor(age, furColor, languageSpoken) {
    super(age, furColor);
    this.languageSpoken = languageSpoken;
  }

  speak() { /* ... */ }
}
```



### メソッドチェーンを利用すること

これは、JavaScriptの中で非常に有用なパターンで、jQueryやLodashのような多くのライブラリの中でみることができます。
それは、コードを表現力を豊かにし、冗長であることを少なくします。
その理由から、私は、「メソッドチェーンを使って、あなたのコードがどれくらい綺麗になるか見てください。」と言います。
クラスの関数の中の全ての関数の終わりで、単純に`this`を返すことで、クラスの中にあるメソッドをチェーンすることができます。

**Bad:**
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

const car = new Car('Ford','F-150','red');
car.setColor('pink');
car.save();
```

**Good:**
```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // NOTE: チェーンするためにthisを返します
    return this;
  }

  setModel(model) {
    this.model = model;
    // NOTE: チェーンするためにthisを返します
    return this;
  }

  setColor(color) {
    this.color = color;
    // NOTE: チェーンするためにthisを返します
    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // NOTE: チェーンするためにthisを返します
    return this;
  }
}

const car = new Car('Ford','F-150','red');
  .setColor('pink')
  .save();
```


### 継承よりコンポジション（組み合わせ）を好む

有名なGang of Four(四人組)による[*デザインパターン*](https://en.wikipedia.org/wiki/Design_Patterns)のように、
可能な場所では継承よりもコンポジションを優先するべきです。
継承を利用する良い理由もコンポジションを利用する良い理由もたくさんあります。
この章の要点は、もしあなたが本能的に継承を使うように心が動くのであれば、コンポジションがその問題をよりよくモデル化できるかどうか考えてみてください。
いくつかの場合、それができます。


あなたは、「いつ継承を使うべきか？」について疑問に思うかもしれません。
それは、手元にある問題に依存します。しかし、これらは継承がコンポジションよりも意味をなす場合のまともなリストです。

1. 継承が「had-a(訳注：「A had a B」で「AはBを含んでいる」の意味)」ではなくて「is-a(訳注：「A is a B」で「AはBである」の意味)」を表している場合(Human->Animal(人間は動物である) と User->UserDetails(人は属性情報を含んでいる))
2. 基底クラスからコードを再利用できる(人は動物のように動くことができる)
3. 基底クラスを変更することで、派生クラスを全体的に変更したい(全ての動物の移動中の消費カロリーを変更する)

**Bad:**
```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// よくない。なぜなら、従業員(Employee)は税情報を持っている。しかし、従業員税情報(EmployeeTaxData)は従業員ではない
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**Good:**
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
### 単一責任の原則(SRP)

クリーンコードに記載されているように、「クラスが変更される理由は、1つ以上あってはならない」。
多くの機能を詰め込んだ超満員のクラスは、フライトで1つのスーツケースしか持てない場合などは魅力的に見えます。
これに関する問題は、そのクラスが概念的に一貫性が乏しく、さまざなな理由により変更ことが多くあるからです。
クラスを変更することに掛かる時間が最小限にすることが重要です。
それが重要なのは、あまりにも多くの機能が1つのクラスにあって、その中のひとまとまりを変更する場合、コードベースの中の他の依存しているモジュールに対してどのように影響を与えるか理解することが難しいことになるからです。

**Bad:**
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

**Good:**
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


### オープン・クローズドの原則(OCP)

Bertrand Meyer(バートランド・メイヤー)が記したように、「ソフトウエアの構成要素(クラス、モジュール、関数など)は、拡張に対してオープンで、変更に対してはクローズであるべき」
それはどう言う意味でしょうか？
この原則は基本的に、ユーザーに対して既存のコードを変更することなく、新しい機能を加えることができるようにするべきと記されている。

**Bad:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
  }
}

class HttpRequester {
  constructor(adapter) {
    this.adapter = adapter;
  }

  fetch(url) {
    if (this.adapter.name === 'ajaxAdapter') {
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === 'httpNodeAdapter') {
      return makeHttpCall(url).then((response) => {
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

**Good:**
```javascript
class AjaxAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'ajaxAdapter';
  }

  request(url) {
    // request and return promise
  }
}

class NodeAdapter extends Adapter {
  constructor() {
    super();
    this.name = 'nodeAdapter';
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
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```


### リスコフの置換原則 (LSP)

これは非常に単縦なコンセプトと呼ぶには恐れ多いです。
正式には、次のように定義されています。
「もしSがTの派生型の場合、T型のオブジェクトは、そのプログラムの特性(正確さ、作業能力、など)を損なうことなく、S型のオブジェクトで置き換えることができなければならない(いういなれば、S型のオブジェクトはT型のオブジェクトと置換することができる)」
これでもさらにやっかいな定義です。

これのもっとも良い説明は、もし親クラスと子クラスがある場合、親クラスと子クラスは異なる振る舞いを起こすことなく、交互に利用することができるということです。
まだ混乱しているかもしれないので、古典的な正方形と長方形の例を見てましょう。
数学的には、正方形は長方形ですが、もし継承による「is-a(AはBである)」の関係を利用している場合、容易に問題に陥ります。

**Bad:**
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
  rectangles.forEach((rectangle) => {
    rectangle.setWidth(4);
    rectangle.setHeight(5);
    const area = rectangle.getArea(); //だめ！正方形に25が返ってしまう、20になるべきです。
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**Good:**
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
  shapes.forEach((shape) => {
    const area = shape.getArea();
    shape.render(area);
  });
}

const shapes = [new Rectangle(4, 5), new Rectangle(4, 5), new Square(5)];
renderLargeShapes(shapes);
```


### インターフェイス分離の原則 (ISP)

JavaScriptはインターフェイスを持っていないため、この原則は他のように厳密には適用されません。
しかしながら、これは重要かつ、JavaScriptの欠落した型システムにも関連しています。

ISPは「クライアントに、彼らが利用していないインターフェイスへの依存を強要してはならない」と記されています。
JavaScriptにおいては、ダックタイピングのため、インターフェイスは暗黙的な契約です。

このJavaScriptでこの原則を説明を見せるための良い例は、巨大な設定オブジェクトが必要なクラスです。
クライアントに巨大な数のオプション設定を要求しないことが効果的です。なぜなら、多くの場合すべての設定は必要ないからです。
それらを任意にすることが「肥ったインターフェイス」を持つことを防ぐために有効です。

**Bad:**
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
  rootNode: document.getElementsByTagName('body'),
  animationModule() {} // ほとんどの場合、アニメーションを必要としていない
  // ...
});

```

**Good:**
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
  rootNode: document.getElementsByTagName('body'),
  options: {
    animationModule() {}
  }
});
```


### 依存性逆転の原則 (DIP)

この原則では2つの重要なことを述べています。
1. 上位のモジュールは下位のモジュールに依存してはならない。それぞれは、抽象に依存するべき。
2. 抽象は実装に依存してはいけない、実装は抽象に依存するべき。


これははじめの内は理解することが難しですが、もしAngularJSを触ったことがある人であれば、依存性注入(DI)と言う形でその実装をみたことがあるでしょう。
それらは同一のコンセプトではありませんが、DIPは上位のモジュールが下位のモジュールの実装を知り、それらを設定することを保ちます（え？保つ？逆じゃないの？？）。
このことの巨大なメリットは、モジュール間の密結合を削減することです。
密結合することは、非常に悪い開発のパターンです。なぜなら、それはコードをリファクタしにくくするからです。


以前に述べたように、JavaScriptはインターフェイスを持たないため、その抽象とは暗黙の契約の上に依存しています。
すなわち、あるオブジェクトやクラスのメソッドやプロパティは、他のオブジェクトやクラスにさらけ出されています。
下の例の中の暗黙の契約とは、全ての `InventoryTracker` リクエストモジュールは、 `requestItems` メソッドを持っていることです。

**Bad:**
```javascript
class InventoryRequester {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryTracker {
  constructor(items) {
    this.items = items;

    // だめ！ある特別なリクエスト実装に依存したものを持っている
    // `request`のような、リクエストメソッドに依存したrequestItemsだけを持つべき
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(['apples', 'bananas']);
inventoryTracker.requestItems();
```

**Good:**
```javascript
class InventoryTracker {
  constructor(items, requester) {
    this.items = items;
    this.requester = requester;
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

class InventoryRequesterV1 {
  constructor() {
    this.REQ_METHODS = ['HTTP'];
  }

  requestItem(item) {
    // ...
  }
}

class InventoryRequesterV2 {
  constructor() {
    this.REQ_METHODS = ['WS'];
  }

  requestItem(item) {
    // ...
  }
}

// 外部から依存関係を構築しそれを注入することで、リクエストモジュールを
// 新しいWebSocketを使ったイケているものに置き換えることができる。
const inventoryTracker = new InventoryTracker(['apples', 'bananas'], new InventoryRequesterV2());
inventoryTracker.requestItems();
```


## **テスト**

テストはリリースするよりも大事なことです。
もしテストがなかったり不十分だった場合、コードをリリースするたびに、何も壊れていないことを確かめることはできません。
何をもって十分な量であるかを決定することはチームに任されていますが、(すべての文や分岐に対して)100%のカバレッジを持つことは、非常に高い信頼性と開発者の安心を達成する方法です。
この意味することは、あなたは良いテストフレームワークを持つことに加えて、[良いカバレッジツール](http://gotwarlost.github.io/istanbul/)も使うことが必要だということです。

テストを書かない理由はありません。ここに[多くの優れたJSテストフレームワーク](http://jstherightway.org/#testing-tools)があるので、チームが好むものを一つ見つけてください。
チームに対して一つのフレームワークを見つけた場合は、導入する全ての機能・モジュールごとに、常にテストを書くことを目指します。
もし、あなたが気に入っている方法がテスト駆動開発(TDD)であれば、それは素晴らしいことです。
しかし重要なポイントは、なにかの機能をリリースする前にカバレッジのゴールに達成するか、すでに存在するものをリファクタリングすることを確実にすることだけです。

### テストごとに単純なコンセプトを持つこと

**Bad:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles date boundaries', () => {
    let date;

    date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);

    date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);

    date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```

**Good:**
```javascript
import assert from 'assert';

describe('MakeMomentJSGreatAgain', () => {
  it('handles 30-day months', () => {
    const date = new MakeMomentJSGreatAgain('1/1/2015');
    date.addDays(30);
    assert.equal('1/31/2015', date);
  });

  it('handles leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2016');
    date.addDays(28);
    assert.equal('02/29/2016', date);
  });

  it('handles non-leap year', () => {
    const date = new MakeMomentJSGreatAgain('2/1/2015');
    date.addDays(28);
    assert.equal('03/01/2015', date);
  });
});
```


## **同期処理**
### コールバックではなく、Promiseを使う

コールバックは簡潔ではありません、そしてそれらは過剰な量のネストを引き起こします。
ES2015/ES6ではPromiseがグローバルに組み込まれています。それらを使いましょう！

**Bad:**
```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin', (requestErr, response) => {
  if (requestErr) {
    console.error(requestErr);
  } else {
    writeFile('article.html', response.body, (writeErr) => {
      if (writeErr) {
        console.error(writeErr);
      } else {
        console.log('File written');
      }
    });
  }
});

```

**Good:**
```javascript
import { get } from 'request';
import { writeFile } from 'fs';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```


### Async/AwaitはPromiseよりさらに簡潔です

Promiseはコールバックに対しては非常に簡潔ですが、S2017/ES8はより簡潔な解決案としてasyncとawaitを引き連れてきました。
あなたが必要なことは`async`キーワードを関数の先頭につけることです。そして`then`で関数を連結することなく、ロジックを命令的に書くことができます。
あなたが、今日のES2017/ES8機能の恩恵を受けたい場合は、これを使ってください。

**Bad:**
```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin')
  .then((response) => {
    return writeFile('article.html', response);
  })
  .then(() => {
    console.log('File written');
  })
  .catch((err) => {
    console.error(err);
  });

```

**Good:**
```javascript
import { get } from 'request-promise';
import { writeFile } from 'fs-promise';

async function getCleanCodeArticle() {
  try {
    const response = await get('https://en.wikipedia.org/wiki/Robert_Cecil_Martin');
    await writeFile('article.html', response);
    console.log('File written');
  } catch(err) {
    console.error(err);
  }
}
```



## **エラーハンドリング**

例外が発生することは良いことです！この意味は、ランタイムがあなたのプログラムが何かおかしいことを正常に突き止めたということです。
それは、関数の実行を直近のスタックで停止し、そのプロセスを停止し(ノード中)、コンソールのスタックトレースを通じてあなたに知らせてくれます。

### 例外が捕らえられたことを無視しない

例外を捕捉して何もしないということは、あなたがそのエラーを修正したり、エラーが言ったことに対応したりすることができません。
コンソール(`console.log`)にエラーを出力することは、頻繁にコンソール出力された海に埋もれてしまうため、それほど良いことではありません。
コードの一部を `try/catch` で囲うということは、そこでエラー発生するかもしれないということを意味します。
したがって、エラーが発生した時のために、なにかの対策をしたり、コードの行き先を作らなければなりません。

**Bad:**
```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**Good:**
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

### 失敗したpromiseを無視しない

同じ理由により、`try/catch`にて発生した例外を無視するべきではありません。

**Bad:**
```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    console.log(error);
  });
```

**Good:**
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




## **フォーマット**

フォーマットは主観的です。ここにある多くのルールと同様に、あなたが従わなければならないような、厳格でがんじがらめのルールはありません。
ここに自動化するための[多くのツール](http://standardjs.com/rules.html)があります。
1つを使いましょう！エンジニアがフォーマットについて議論することは、時間とお金の無駄です。

自動フォーマット設定の対象にならないもの(インデント、タブ vs スペース、ダブル vs シングルクォートなど)については、ここでいくつかのガイダンスを見てください。


### 一貫性を持った大文字を利用すること

JavaScriptには型がありません。そのため大文字は変数や関数などについて多くのことを教えてくれます。
これらのルールは主観的なので、あなたのチームが望んているものを選ぶことができます。
ここでのポイントは、あなたが選んだもの全てについて、一貫性を持たせてくださいということだけです。

**Bad:**
```javascript
const DAYS_IN_WEEK = 7;
const daysInMonth = 30;

const SONGS = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const ARTISTS = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restore_database() {}

class animal {}
class Alpaca {}
```

**Good:**
```javascript
const DAYS_IN_WEEK = 7;
const DAYS_IN_MONTH = 30;

const songs = ['Back In Black', 'Stairway to Heaven', 'Hey Jude'];
const artists = ['ACDC', 'Led Zeppelin', 'The Beatles'];

function eraseDatabase() {}
function restoreDatabase() {}

class Animal {}
class Alpaca {}
```



### 関数の呼び出し元と呼び出し先は近くにあること

関数は他を呼び出す場合、それらをソースコードのなかの垂直方向で近くにおくようにしてください。
理想的には、呼び出し元を呼び出し先の上においてください。私たちは新聞のように、コードを上から下に読む傾向があります。
このため、あなたのコードをこのように読ませるようにしてください。

**Bad:**
```javascript
class PerformanceReview {
  constructor(employee) {
    this.employee = employee;
  }

  lookupPeers() {
    return db.lookup(this.employee, 'peers');
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
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

**Good:**
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
    return db.lookup(this.employee, 'peers');
  }

  getManagerReview() {
    const manager = this.lookupManager();
  }

  lookupManager() {
    return db.lookup(this.employee, 'manager');
  }

  getSelfReview() {
    // ...
  }
}

const review = new PerformanceReview(employee);
review.perfReview();
```



## **コメント**

### ビジネスロジックが複雑なものにのみコメントすること

コメントは弁明です、必須ではありません。良いコードは *ほとんどが* ドキュメントそのものです。

**Bad:**
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
    hash = ((hash << 5) - hash) + char;
    // Convert to 32-bit integer
    hash &= hash;
  }
}
```

**Good:**
```javascript

function hashIt(data) {
  let hash = 0;
  const length = data.length;

  for (let i = 0; i < length; i++) {
    const char = data.charCodeAt(i);
    hash = ((hash << 5) - hash) + char;

    // Convert to 32-bit integer
    hash &= hash;
  }
}

```


### コメントアウトしたコードをコードベースに残さない

バージョン管理があることがその理由です。古いコードは履歴に残しましょう。

**Bad:**
```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**Good:**
```javascript
doStuff();
```


### 日記のようなコメントは持たない

バージョン管理を使うことを覚えてほしい！使われていないコード、コメント付きのコード、特に日記の様なコメント。
履歴を取得するためには `git log` を使ってください！

**Bad:**
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

**Good:**
```javascript
function combine(a, b) {
  return a + b;
}
```


### 位置どりのためのマーカーを避ける

それらは通常はただのノイズです。
関数や変数に適切なインデントとフォーマットを施すことで、コードに視覚的な構造を与えることができます。

**Bad:**
```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function() {
  // ...
};
```

**Good:**
```javascript
$scope.model = {
  menu: 'foo',
  nav: 'bar'
};

const actions = function() {
  // ...
};
```
