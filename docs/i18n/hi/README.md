## क्लीन कोड जावास्क्रिप्ट <!-- {docsify-ignore} -->

> author: [@ryanmcdermott](https://github.com/ryanmcdermott/clean-code-javascript) | translator: [@khushipy](https://github.com/khushipy/clean-code-javascript)

## परिचय

![सॉफ्टवेयर की गुणवत्ता का आकलन करते हुए मज़ाकिया चित्र, जिसमें दिखाया गया है कि कोड पढ़ते समय आपने कितनी बार गाली दी](https://www.osnews.com/images/comics/wtfm.jpg)

सॉफ्टवेयर इंजीनियरिंग के सिद्धांत, जो रॉबर्ट सी. मार्टिन की किताब  
[_क्लीन कोड_](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)  
से लिए गए हैं, और जावास्क्रिप्ट के लिए अनुकूलित किए गए हैं।

यह कोई स्टाइल गाइड नहीं है। यह गाइड है ऐसा सॉफ़्टवेयर लिखने के लिए जो  
[पढ़ने योग्य, पुन: उपयोग योग्य और पुनः संरचनाकृत करने योग्य](/3rs-of-software-architecture.md) [(स्रोत)](https://github.com/ryanmcdermott/3rs-of-software-architecture) हो — विशेषकर जावास्क्रिप्ट में।

यहाँ दिए गए हर सिद्धांत को सख्ती से मानना ज़रूरी नहीं है, और शायद सभी पर हर कोई सहमत भी नहीं होगा।  
ये केवल दिशानिर्देश हैं — लेकिन ऐसे जो वर्षों के सामूहिक अनुभव से तैयार किए गए हैं, खासतौर पर _क्लीन कोड_ के लेखकों द्वारा।

हमारा सॉफ़्टवेयर इंजीनियरिंग का पेशा अभी केवल 50 वर्षों से भी थोड़ा पुराना है, और हम अब भी बहुत कुछ सीख रहे हैं।  
जिस दिन सॉफ़्टवेयर आर्किटेक्चर, पारंपरिक आर्किटेक्चर जितना पुराना होगा — शायद तब हमारे पास भी कठोर नियम होंगे।  
अभी के लिए, ये दिशानिर्देश आपके और आपकी टीम के द्वारा लिखे गए जावास्क्रिप्ट कोड की गुणवत्ता को परखने का आधार हो सकते हैं।

एक बात और: इन सिद्धांतों को जान लेने से आप तुरंत बेहतर सॉफ़्टवेयर डेवलपर नहीं बन जाते,  
और कई वर्षों तक इनके साथ काम करने के बाद भी आपसे गलतियाँ हो सकती हैं।  
हर कोड की शुरुआत एक पहले ड्राफ्ट के रूप में होती है — जैसे गीली मिट्टी जिसे अंतिम रूप देना बाकी हो।  
अंत में, जब हम इसे अपने साथियों के साथ रिव्यू करते हैं, तब हम उसमें से खामियाँ हटाते हैं।  
अपने पहले ड्राफ्ट को लेकर निराश न हों — कोड को सुधारें, खुद को नहीं!

## **वैरिएबल्स (Variables)**

### अर्थपूर्ण और उच्चारण योग्य वैरिएबल नामों का उपयोग करें

**गलत:**

```javascript
const yyyymmdstr = moment().format("YYYY/MM/DD");
```

**सही:**

```javascript
const currentDate = moment().format("YYYY/MM/DD");
```

### एक ही प्रकार के वैरिएबल के लिए समान शब्दावली का उपयोग करें

**गलत:**

```javascript
getUserInfo();
getClientData();
getCustomerRecord();
```

**सही:**

```javascript
getUser();
```

### खोज योग्य नामों का उपयोग करें

हम जितना कोड लिखेंगे, उससे कहीं अधिक पढ़ेंगे। यह ज़रूरी है कि जो कोड हम लिखें वह पढ़ने और खोजने में आसान हो।
अगर आप ऐसे वैरिएबल्स को नाम नहीं देंगे जिनका मतलब समझने में मदद मिलती है, तो यह पाठकों के लिए नुकसानदायक होगा।
अपने नामों को खोज योग्य बनाएं। ऐसे टूल्स जैसे
[buddy.js](https://github.com/danielstjules/buddy.js) और
[ESLint](https://github.com/eslint/eslint/blob/660e0918933e6e7fede26bc675a0763a6b357c94/docs/rules/no-magic-numbers.md)
आपको बिना नाम वाले कॉन्स्टेंट्स पहचानने में मदद कर सकते हैं।

**गलत:**

```javascript
// 86400000 किस लिए है समझ नहीं आता?
setTimeout(blastOff, 86400000);
```

**सही:**

```javascript
// उन्हें कैपिटल अक्षरों में नाम देकर घोषित करें।
const MILLISECONDS_PER_DAY = 60 * 60 * 24 * 1000; //86400000;

setTimeout(blastOff, MILLISECONDS_PER_DAY);
```

### व्याख्यात्मक Variables का उपयोग करें

**गलत:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
saveCityZipCode(
  address.match(cityZipCodeRegex)[1],
  address.match(cityZipCodeRegex)[2]
);
```

**सही:**

```javascript
const address = "One Infinite Loop, Cupertino 95014";
const cityZipCodeRegex = /^[^,\\]+[,\\\s]+(.+?)\s*(\d{5})?$/;
const [_, city, zipCode] = address.match(cityZipCodeRegex) || [];
saveCityZipCode(city, zipCode);
```

### मानसिक मानचित्रण से बचें

स्पष्ट होना बेहतर है बजाय अस्पष्ट होने के।

**गलत:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach((l) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  // रुकिए, `l` का मतलब क्या था?
  dispatch(l);
});
```

**सही:**

```javascript
const locations = ["Austin", "New York", "San Francisco"];
locations.forEach((location) => {
  doStuff();
  doSomeOtherStuff();
  // ...
  // ...
  // ...
  dispatch(location);
});
```

### अनावश्यक संदर्भ न जोड़ें

अगर आपका क्लास/ऑब्जेक्ट नाम पहले से ही कुछ बता रहा है, तो उस जानकारी को नाम में दोहराने की जरूरत नहीं है।

**गलत:**

```javascript
const Car = {
  carMake: "Honda",
  carModel: "Accord",
  carColor: "Blue",
};

function paintCar(car, color) {
  car.carColor = color;
}
```

**सही:**

```javascript
const Car = {
  make: "Honda",
  model: "Accord",
  color: "Blue",
};

function paintCar(car, color) {
  car.color = color;
}
```

### शॉर्ट सर्किटिंग या कंडीशनल्स की जगह डिफ़ॉल्ट पैरामीटर्स का उपयोग करें

डिफ़ॉल्ट पैरामीटर्स का उपयोग करना अक्सर शॉर्ट सर्किटिंग से अधिक स्पष्ट और साफ़ होता है। ध्यान रखें कि ये केवल `undefined` आर्ग्युमेंट्स के लिए डिफ़ॉल्ट वैल्यू देंगे। अन्य "falsy" वैल्यू जैसे `''`, `""`, `false`, `null`, `0`, और `NaN` को डिफ़ॉल्ट वैल्यू से नहीं बदला जाएगा।

**गलत:**

```javascript
function createMicrobrewery(name) {
  const breweryName = name || "Hipster Brew Co.";
  // ...
}
```

**सही:**

```javascript
function createMicrobrewery(name = "Hipster Brew Co.") {
  // ...
}
```

## **फ़ंक्शन (Functions)**

### फ़ंक्शन आर्ग्युमेंट्स (आदर्श रूप से 2 या उससे कम)

फ़ंक्शन पैरामीटर्स की संख्या सीमित रखना बहुत महत्वपूर्ण है क्योंकि इससे आपके फंक्शन का परीक्षण करना आसान होता है। तीन से अधिक पैरामीटर्स होने पर आपको हर संभव संयोजन का परीक्षण करना पड़ सकता है।

एक या दो आर्ग्युमेंट्स आदर्श होते हैं, और तीन से बचना चाहिए। यदि तीन से अधिक आवश्यक हों, तो उन्हें एक उच्च-स्तरीय ऑब्जेक्ट में समेकित करें। आमतौर पर, यदि बहुत अधिक आर्ग्युमेंट्स हैं, तो संभवतः आपका फंक्शन बहुत अधिक कार्य कर रहा है।

JavaScript आपको बिना अधिक क्लास बायलरप्लेट के फ्लाई पर ऑब्जेक्ट बनाने की अनुमति देता है, इसलिए आप एक ऑब्जेक्ट का उपयोग कर सकते हैं जब आपको कई पैरामीटर्स की आवश्यकता हो।

डेस्ट्रक्चरिंग सिंटैक्स (ES2015/ES6) के उपयोग से यह स्पष्ट हो जाता है कि फंक्शन किन प्रॉपर्टीज की अपेक्षा कर रहा है। इसके कई लाभ हैं:

1. फंक्शन सिग्नेचर को देखकर तुरंत समझ आता है कि कौन-कौन सी प्रॉपर्टीज उपयोग हो रही हैं।
2. यह नामांकित पैरामीटर्स जैसा व्यवहार प्रदान करता है।
3. डेस्ट्रक्चरिंग प्रिमिटिव वैल्यूज़ को क्लोन कर देती है, जिससे साइड इफेक्ट्स कम होते हैं। (नोट: ऑब्जेक्ट और एरे क्लोन नहीं होते)
4. Linters आपको अप्रयुक्त प्रॉपर्टीज़ की चेतावनी दे सकते हैं, जो सामान्य स्थिति में संभव नहीं होता।

**गलत:**

```javascript
function createMenu(title, body, buttonText, cancellable) {
  // ...
}

createMenu("Foo", "Bar", "Baz", true);
```

**सही:**

```javascript
function createMenu({ title, body, buttonText, cancellable }) {
  // ...
}

createMenu({
  title: "Foo",
  body: "Bar",
  buttonText: "Baz",
  cancellable: true,
});
```

### फ़ंक्शन को केवल एक ही काम करना चाहिए

यह सॉफ्टवेयर इंजीनियरिंग का सबसे महत्वपूर्ण नियम है। जब फ़ंक्शन एक से अधिक कार्य करता है, तो उसे समझना, टेस्ट करना और रीफ़ैक्टर करना कठिन हो जाता है। अगर आप किसी फ़ंक्शन को केवल एक ही कार्य तक सीमित रखते हैं, तो आपका कोड साफ़ और आसानी से समझने योग्य होगा।

**गलत:**

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

**सही:**

```javascript
function emailActiveClients(clients) {
  clients.filter(isActiveClient).forEach(email);
}

function isActiveClient(client) {
  const clientRecord = database.lookup(client);
  return clientRecord.isActive();
}
```

### फ़ंक्शन का नाम उसके कार्य को दर्शाना चाहिए

**गलत:**

```javascript
function addToDate(date, month) {
  // ...
}

const date = new Date();

// फ़ंक्शन के नाम से यह समझना मुश्किल है कि क्या जोड़ा जा रहा है
addToDate(date, 1);
```

**सही:**

```javascript
function addMonthToDate(month, date) {
  // ...
}

const date = new Date();
addMonthToDate(1, date);
```

### फ़ंक्शन केवल एक स्तर के अमूर्तन (abstraction) पर होने चाहिए

जब आपके फ़ंक्शन में एक से अधिक अमूर्तन स्तर होते हैं, तो वह आमतौर पर बहुत अधिक कार्य कर रहा होता है।
फ़ंक्शनों को विभाजित करना पुन: उपयोग और परीक्षण को आसान बनाता है।

**गलत:**

```javascript
function parseBetterJSAlternative(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
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

**सही:**

```javascript
function parseBetterJSAlternative(code) {
  const tokens = tokenize(code);
  const syntaxTree = parse(tokens);
  syntaxTree.forEach((node) => {
    // parse...
  });
}

function tokenize(code) {
  const REGEXES = [
    // ...
  ];

  const statements = code.split(" ");
  const tokens = [];
  REGEXES.forEach((REGEX) => {
    statements.forEach((statement) => {
      tokens.push(/* ... */);
    });
  });

  return tokens;
}

function parse(tokens) {
  const syntaxTree = [];
  tokens.forEach((token) => {
    syntaxTree.push(/* ... */);
  });

  return syntaxTree;
}
```

### डुप्लिकेट कोड को हटाएं

अपना पूरा प्रयास करें कि डुप्लिकेट कोड न हो। डुप्लिकेट कोड खराब होता है क्योंकि किसी लॉजिक को बदलने की आवश्यकता होने पर आपको एक से अधिक स्थानों पर बदलाव करना पड़ता है।

कल्पना कीजिए कि आप एक रेस्टोरेंट चलाते हैं और अपनी इन्वेंट्री (टमाटर, प्याज, मसाले आदि) को ट्रैक करते हैं। यदि आपके पास कई सूचियाँ हैं, तो हर सूची को अपडेट करना होगा जब कोई डिश परोसी जाए। लेकिन यदि आपके पास केवल एक सूची है, तो केवल एक स्थान पर अपडेट करना होता है।

कई बार आपके पास डुप्लिकेट कोड इसलिए होता है क्योंकि आपके पास दो या अधिक चीज़ें होती हैं जिनमें थोड़ा बहुत अंतर होता है। इन अंतरों के कारण आपको अलग-अलग फ़ंक्शन बनाने पड़ते हैं। डुप्लिकेट कोड को हटाने का मतलब है कि आप एक ऐसा abstraction बनाएं जो उन सभी केसों को एक ही फ़ंक्शन से संभाल सके।

सही abstraction बनाना बेहद ज़रूरी है। गलत abstraction डुप्लिकेट कोड से भी ज़्यादा नुकसानदायक हो सकता है। इसलिए यदि आप एक सही abstraction बना सकते हैं, तो ज़रूर बनाएं। "खुद को दोहराएं नहीं", वरना आपको एक ही चीज़ के लिए कई स्थानों पर बदलाव करना पड़ेगा।

**गलत:**

```javascript
function showDeveloperList(developers) {
  developers.forEach((developer) => {
    const expectedSalary = developer.calculateExpectedSalary();
    const experience = developer.getExperience();
    const githubLink = developer.getGithubLink();
    const data = {
      expectedSalary,
      experience,
      githubLink,
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
      portfolio,
    };

    render(data);
  });
}
```

**सही:**

```javascript
function showEmployeeList(employees) {
  employees.forEach((employee) => {
    const expectedSalary = employee.calculateExpectedSalary();
    const experience = employee.getExperience();

    const data = {
      expectedSalary,
      experience,
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

### Object.assign के साथ डिफ़ॉल्ट ऑब्जेक्ट सेट करें

**गलत:**

```javascript
const menuConfig = {
  title: null,
  body: "Bar",
  buttonText: null,
  cancellable: true,
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

**सही:**

```javascript
const menuConfig = {
  title: "Order",
  // उपयोगकर्ता ने 'body' key शामिल नहीं किया है
  buttonText: "Send",
  cancellable: true,
};

function createMenu(config) {
  let finalConfig = Object.assign(
    {
      title: "Foo",
      body: "Bar",
      buttonText: "Baz",
      cancellable: true,
    },
    config
  );
  return finalConfig;
  // config अब होगा: {title: "Order", body: "Bar", buttonText: "Send", cancellable: true}
  // ...
}

createMenu(menuConfig);
```

### फ़ंक्शन पैरामीटर में फ़्लैग्स का उपयोग न करें

फ्लैग यह दर्शाते हैं कि आपका फ़ंक्शन एक से अधिक काम कर रहा है। लेकिन एक फ़ंक्शन को केवल एक ही कार्य करना चाहिए। अगर आपका फ़ंक्शन boolean फ़्लैग पर आधारित अलग-अलग रास्ते ले रहा है, तो उन कार्यों को अलग-अलग फ़ंक्शनों में बाँट दीजिए।

**गलत:**

```javascript
function createFile(name, temp) {
  if (temp) {
    fs.create(`./temp/${name}`);
  } else {
    fs.create(name);
  }
}
```

**सही:**

```javascript
function createFile(name) {
  fs.create(name);
}

function createTempFile(name) {
  createFile(`./temp/${name}`);
}
```

### साइड इफेक्ट्स से बचें (भाग 1)

अगर कोई फ़ंक्शन सिर्फ इनपुट ले और आउटपुट दे, और कुछ न करे — जैसे कि फ़ाइल लिखना या कोई ग्लोबल वैरिएबल बदलना — तो उसे "pure function" कहते हैं।

लेकिन असल प्रोग्राम में कभी-कभी साइड इफेक्ट्स की ज़रूरत होती है, जैसे कि फ़ाइल सेव करना। पर इन्हें सेंट्रलाइज़ करके करें। एक ही सर्विस से करवाएं, कई जगहों से नहीं।

**गलत:**

```javascript
let name = "Ryan McDermott";

function splitIntoFirstAndLastName() {
  name = name.split(" ");
}

splitIntoFirstAndLastName();

console.log(name); // ['Ryan', 'McDermott'];
```

**सही:**

```javascript
function splitIntoFirstAndLastName(name) {
  return name.split(" ");
}

const name = "Ryan McDermott";
const newName = splitIntoFirstAndLastName(name);

console.log(name); // 'Ryan McDermott';
console.log(newName); // ['Ryan', 'McDermott'];
```

### साइड इफेक्ट्स से बचें (भाग 2)

JavaScript में arrays और objects **mutable** होते हैं यानी उनमें बदलाव किया जा सकता है। इसलिए जब आप इन्हें किसी फ़ंक्शन को पास करते हैं, तो बहुत सावधानी रखें।

मान लीजिए एक `cart` array है, और कोई फ़ंक्शन उसमें सामान जोड़ता है। अगर वही `cart` कहीं और भी इस्तेमाल हो रहा है, तो उसका व्यवहार बदल सकता है और बग आ सकता है।

इसका हल यह है कि हर बार array को **clone** करें और फिर उसमें बदलाव करें। इससे ओरिजिनल array वैसा का वैसा रहेगा।

**गलत:**

```javascript
const addItemToCart = (cart, item) => {
  cart.push({ item, date: Date.now() });
};
```

**सही:**

```javascript
const addItemToCart = (cart, item) => {
  return [...cart, { item, date: Date.now() }];
};
```

### ग्लोबल फ़ंक्शन को न छेड़ें

JavaScript में ग्लोबल ऑब्जेक्ट्स या prototypes को बदलना ख़तरनाक है क्योंकि इससे दूसरे कोड या लाइब्रेरीज़ में टकराव हो सकता है।

अगर आप `Array.prototype` को बदल देते हैं, और कोई दूसरी लाइब्रेरी भी वही method जोड़ती है तो runtime errors आ सकते हैं।

इसलिए `Array` को extend करें और उसमें methods डालें।

**गलत:**

```javascript
Array.prototype.diff = function diff(comparisonArray) {
  const hash = new Set(comparisonArray);
  return this.filter((elem) => !hash.has(elem));
};
```

**सही:**

```javascript
class SuperArray extends Array {
  diff(comparisonArray) {
    const hash = new Set(comparisonArray);
    return this.filter((elem) => !hash.has(elem));
  }
}
```

### इम्पेरेटिव प्रोग्रामिंग की बजाय फंक्शनल प्रोग्रामिंग को प्राथमिकता दें

JavaScript एक फंक्शनल भाषा नहीं है जैसे कि Haskell, लेकिन इसमें एक फंक्शनल स्वाद है। फंक्शनल भाषाएं ज़्यादा क्लीन और टेस्ट करना आसान होती हैं। जब भी संभव हो, इस स्टाइल को अपनाएं।

**ग़लत:**

```javascript
const programmerOutput = [
  { name: "Uncle Bobby", linesOfCode: 500 },
  { name: "Suzie Q", linesOfCode: 1500 },
  { name: "Jimmy Gosling", linesOfCode: 150 },
  { name: "Gracie Hopper", linesOfCode: 1000 },
];

let totalOutput = 0;

for (let i = 0; i < programmerOutput.length; i++) {
  totalOutput += programmerOutput[i].linesOfCode;
}
```

**सही:**

```javascript
const programmerOutput = [
  { name: "Uncle Bobby", linesOfCode: 500 },
  { name: "Suzie Q", linesOfCode: 1500 },
  { name: "Jimmy Gosling", linesOfCode: 150 },
  { name: "Gracie Hopper", linesOfCode: 1000 },
];

const totalOutput = programmerOutput.reduce(
  (totalLines, output) => totalLines + output.linesOfCode,
  0
);
```

### कंडीशन्स को इनकैप्सुलेट करें

**ग़लत:**

```javascript
if (fsm.state === "fetching" && isEmpty(listNode)) {
  // ...
}
```

**सही:**

```javascript
function shouldShowSpinner(fsm, listNode) {
  return fsm.state === "fetching" && isEmpty(listNode);
}

if (shouldShowSpinner(fsmInstance, listNodeInstance)) {
  // ...
}
```

### नेगेटिव कंडीशन्स से बचें

**ग़लत:**

```javascript
function isDOMNodeNotPresent(node) {
  // ...
}

if (!isDOMNodeNotPresent(node)) {
  // ...
}
```

**सही:**

```javascript
function isDOMNodePresent(node) {
  // ...
}

if (isDOMNodePresent(node)) {
  // ...
}
```

### कंडीशन्स से बचें

**ग़लत:**

```javascript
class Airplane {
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

**सही:**

```javascript
class Airplane {}

class Boeing777 extends Airplane {
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getPassengerCount();
  }
}

class AirForceOne extends Airplane {
  getCruisingAltitude() {
    return this.getMaxAltitude();
  }
}

class Cessna extends Airplane {
  getCruisingAltitude() {
    return this.getMaxAltitude() - this.getFuelExpenditure();
  }
}
```

### टाइप-चेकिंग से बचें (भाग 1)

**ग़लत:**

```javascript
function travelToTexas(vehicle) {
  if (vehicle instanceof Bicycle) {
    vehicle.pedal(this.currentLocation, new Location("texas"));
  } else if (vehicle instanceof Car) {
    vehicle.drive(this.currentLocation, new Location("texas"));
  }
}
```

**सही:**

```javascript
function travelToTexas(vehicle) {
  vehicle.move(this.currentLocation, new Location("texas"));
}
```

### टाइप-चेकिंग से बचें (भाग 2)

**ग़लत:**

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

**सही:**

```javascript
function combine(val1, val2) {
  return val1 + val2;
}
```

### ज़रूरत से ज़्यादा ऑप्टिमाइज़ ना करें

**ग़लत:**

```javascript
for (let i = 0, len = list.length; i < len; i++) {
  // ...
}
```

**सही:**

```javascript
for (let i = 0; i < list.length; i++) {
  // ...
}
```

### डेड कोड हटाएं

**ग़लत:**

```javascript
function oldRequestModule(url) {
  // ...
}

function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

**सही:**

```javascript
function newRequestModule(url) {
  // ...
}

const req = newRequestModule;
inventoryTracker("apples", req, "www.inventory-awesome.io");
```

## **ऑब्जेक्ट्स और डेटा स्ट्रक्चर**

### गेटर्स और सेटर्स का उपयोग करें

ऑब्जेक्ट्स में सीधे प्रॉपर्टी एक्सेस करने की बजाय गेटर्स और सेटर्स का उपयोग करना बेहतर हो सकता है। क्यों? इसके कई कारण हैं:

- जब आपको केवल एक प्रॉपर्टी नहीं बल्कि कुछ और अतिरिक्त काम करने की ज़रूरत हो तो हर जगह कोड बदलने की जरूरत नहीं होती।
- वैलिडेशन को `set` करते समय आसान बनाता है।
- आंतरिक रिप्रेजेंटेशन को छुपाता है।
- गेट और सेट के समय लॉगिंग और एरर हैंडलिंग जोड़ना आसान होता है।
- आप lazy-load कर सकते हैं, जैसे सर्वर से डेटा लाना।

**गलत:**

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

**सही:**

```javascript
function makeBankAccount() {
  // यह private है
  let balance = 0;

  // "getter", जो नीचे return किए गए ऑब्जेक्ट में पब्लिक है
  function getBalance() {
    return balance;
  }

  // "setter", जो नीचे return किए गए ऑब्जेक्ट में पब्लिक है
  function setBalance(amount) {
    // ... वैलिडेशन करें
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

### ऑब्जेक्ट्स में प्राइवेट मेंबर्स रखें

ES5 और इससे नीचे में इसे closures के माध्यम से किया जा सकता है।

**गलत:**

```javascript
const Employee = function (name) {
  this.name = name;
};

Employee.prototype.getName = function getName() {
  return this.name;
};

const employee = new Employee("John Doe");
console.log(`Employee name: ${employee.getName()}`);
delete employee.name;
console.log(`Employee name: ${employee.getName()}`);
```

**सही:**

```javascript
function makeEmployee(name) {
  return {
    getName() {
      return name;
    },
  };
}

const employee = makeEmployee("John Doe");
console.log(`Employee name: ${employee.getName()}`);
delete employee.name;
console.log(`Employee name: ${employee.getName()}`);
```

## **क्लासेस **

### ES5 के प्लेन फ़ंक्शनों की बजाय ES2015/ES6 क्लासेस का उपयोग करें

ES5 में क्लास इनहेरिटेंस, कन्स्ट्रक्शन और मेथड डिफ़िनिशन को पढ़ना और समझना बहुत मुश्किल होता है। अगर आपको इनहेरिटेंस की ज़रूरत है (और ध्यान दें कि शायद आपको इसकी ज़रूरत न भी हो), तो ES2015/ES6 क्लासेस का उपयोग करें। हालांकि, जब तक ज़रूरत न हो, तब तक छोटे फ़ंक्शनों को क्लासेस पर प्राथमिकता दें — जब तक आप अधिक जटिल ऑब्जेक्ट्स की ज़रूरत महसूस न करें।

**गलत:**

```javascript
const Animal = function (age) {
  if (!(this instanceof Animal)) {
    throw new Error("Instantiate Animal with `new`");
  }

  this.age = age;
};

Animal.prototype.move = function move() {};

const Mammal = function (age, furColor) {
  if (!(this instanceof Mammal)) {
    throw new Error("Instantiate Mammal with `new`");
  }

  Animal.call(this, age);
  this.furColor = furColor;
};

Mammal.prototype = Object.create(Animal.prototype);
Mammal.prototype.constructor = Mammal;
Mammal.prototype.liveBirth = function liveBirth() {};

const Human = function (age, furColor, languageSpoken) {
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

**सही:**

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

### मेथड चेनिंग का प्रयोग करें

यह पैटर्न JavaScript में बहुत उपयोगी है और आप इसे कई लाइब्रेरीज़ जैसे jQuery और Lodash में देख सकते हैं।
यह आपके कोड को अभिव्यक्तिपूर्ण (expressive) बनाता है और कम शब्दों वाला (less verbose) भी।

इसी कारण मैं कहता हूँ — मेथड चेनिंग का उपयोग करें और देखिए आपका कोड कितना साफ़ और सुंदर दिखेगा।

आपकी क्लास की प्रत्येक फ़ंक्शन में अंत में बस this रिटर्न करें,
और आप उसी पर आगे के क्लास मेथड्स को चेन कर सकते हैं।

**गलत:**

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

**सही:**

```javascript
class Car {
  constructor(make, model, color) {
    this.make = make;
    this.model = model;
    this.color = color;
  }

  setMake(make) {
    this.make = make;
    // ध्यान दें: चेनिंग के लिए `this` लौटाया जा रहा है

    return this;
  }

  setModel(model) {
    this.model = model;
    // ध्यान दें: चेनिंग के लिए `this` लौटाया जा रहा है

    return this;
  }

  setColor(color) {
    this.color = color;
    // ध्यान दें: चेनिंग के लिए `this` लौटाया जा रहा है

    return this;
  }

  save() {
    console.log(this.make, this.model, this.color);
    // ध्यान दें: चेनिंग के लिए `this` लौटाया जा रहा है

    return this;
  }
}

const car = new Car("Ford", "F-150", "red").setColor("pink").save();
```

### विरासत (Inheritance) के बजाय संघटन (Composition) को प्राथमिकता दें

जैसा कि प्रसिद्ध [_Design Patterns_](https://en.wikipedia.org/wiki/Design_Patterns) किताब में "Gang of Four" द्वारा कहा गया है,
जहाँ संभव हो वहाँ विरासत के बजाय संघटन को प्राथमिकता दें। विरासत का उपयोग करने के बहुत सारे **सही** कारण हैं और संघटन के भी बहुत सारे **सही** कारण हैं।
इस सिद्धांत का मुख्य उद्देश्य यह है कि अगर आप स्वाभाविक रूप से विरासत की ओर सोचते हैं, तो एक बार विचार करें कि क्या संघटन आपकी समस्या को बेहतर तरीके से मॉडल कर सकता है।
कई मामलों में यह बेहतर काम करता है।

आप सोच सकते हैं, "तो फिर विरासत का उपयोग कब करना चाहिए?"  
यह आपके समस्या पर निर्भर करता है, लेकिन नीचे कुछ सामान्य स्थितियाँ हैं जहाँ विरासत, संघटन से बेहतर विकल्प हो सकता है:

1. जब आपका विरासती संबंध "is-a" होता है न कि "has-a"  
   (उदाहरण: Human → Animal सही है, लेकिन User → UserDetails नहीं)।
2. जब आप बेस क्लास से कोड दोबारा उपयोग कर सकते हैं  
   (जैसे सभी जानवरों की तरह मानव भी चल सकते हैं)।
3. जब आप बेस क्लास में बदलाव करके सभी डेराइव्ड क्लास में वैश्विक परिवर्तन करना चाहते हैं  
   (जैसे सभी जानवरों की मूवमेंट पर कैलोरी खर्च बदलना)।

**गलत:**

```javascript
class Employee {
  constructor(name, email) {
    this.name = name;
    this.email = email;
  }

  // ...
}

// गलत because Employees "have" tax data. EmployeeTaxData is not a type of Employee
class EmployeeTaxData extends Employee {
  constructor(ssn, salary) {
    super();
    this.ssn = ssn;
    this.salary = salary;
  }

  // ...
}
```

**सही:**

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

### एकल उत्तरदायित्व सिद्धांत (Single Responsibility Principle - SRP)

जैसा कि _Clean Code_ में कहा गया है,  
"किसी भी क्लास को बदलने का केवल एक ही कारण होना चाहिए।"

किसी क्लास में बहुत सारी कार्यक्षमता भर देना काफी लुभावना हो सकता है —  
जैसे कि जब आपकी फ्लाइट में केवल एक ही सूटकेस ले जाने की अनुमति हो।  
लेकिन इससे समस्या यह होती है कि आपकी क्लास का उद्देश्य अस्पष्ट हो जाता है और उसमें बदलाव के कई कारण बन जाते हैं।

किसी क्लास में बदलाव करने की आवश्यकता को न्यूनतम रखना **सही** है।

ऐसा इसलिए महत्वपूर्ण है क्योंकि अगर बहुत सी कार्यक्षमता एक ही क्लास में डाल दी जाए और आप उसका कोई एक भाग बदलें,  
तो यह समझना मुश्किल हो सकता है कि इसका आपकी कोडबेस में अन्य निर्भर मॉड्यूल्स पर क्या प्रभाव पड़ेगा।

**गलत:**

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

**सही:**

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

### Open/Closed Principle (OCP)

जैसा कि बर्ट्रेंड मेयर ने कहा है:
"सॉफ़्टवेयर एंटिटीज़ (जैसे कि क्लास, मॉड्यूल, फ़ंक्शंस आदि) को विस्तार (extension) के लिए खुला होना चाहिए, लेकिन संशोधन (modification) के लिए बंद होना चाहिए।" इसका क्या मतलब है?

इस सिद्धांत का आशय यह है कि आपको इस तरह से कोड लिखना चाहिए कि जब उपयोगकर्ता नई कार्यक्षमता (functionality) जोड़ना चाहें, तो उन्हें मौजूदा कोड को बदले बिना ऐसा करने का तरीका मिल सके।

सरल शब्दों में:
आपका कोड इस तरह डिज़ाइन होना चाहिए कि उसमें आसानी से नया जोड़ा जा सके, लेकिन पहले से मौजूद कोड को छेड़े बिना।

यह सिद्धांत विशेष रूप से प्लग-इन सिस्टम्स, स्ट्रैटेजी पैटर्न, और इंटरफ़ेस आधारित डिज़ाइन में बहुत उपयोगी होता है।

**गलत:**

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
      return makeAjaxCall(url).then((response) => {
        // transform response and return
      });
    } else if (this.adapter.name === "nodeAdapter") {
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

**सही:**

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
    return this.adapter.request(url).then((response) => {
      // transform response and return
    });
  }
}
```

### लिस्कोव सब्स्टीट्यूशन प्रिंसिपल (LSP)

यह एक डरावना नाम है, लेकिन इसका मतलब बहुत ही सरल है। इसे औपचारिक रूप से इस तरह परिभाषित किया गया है:
"यदि S, T का उपप्रकार (subtype) है, तो T प्रकार के ऑब्जेक्ट्स को S प्रकार के ऑब्जेक्ट्स से बदला जा सकता है (अर्थात् S प्रकार के ऑब्जेक्ट्स, T प्रकार के ऑब्जेक्ट्स की जगह ले सकते हैं) बिना प्रोग्राम की किसी भी वांछनीय विशेषता (सही कार्य करना, निष्पादन, आदि) को बदले या नुकसान पहुँचाए।"

ये परिभाषा थोड़ी डरावनी लग सकती है, लेकिन इसका असल मतलब आसान है।

सरल भाषा में कहें तो, अगर आपके पास एक parent class और एक child class है, तो दोनों को एक-दूसरे की जगह पर उपयोग करने पर भी आपका प्रोग्राम सही काम करता रहे — बिना गलत परिणाम दिए।

यह अब भी थोड़ा भ्रमित कर सकता है, इसलिए चलिए एक क्लासिक उदाहरण देखते हैं: Square-Rectangle।

गणित में, square एक rectangle होता है। लेकिन अगर आप इसे inheritance के ज़रिए "is-a" रिलेशनशिप के रूप में मॉडल करने की कोशिश करते हैं, तो आपको तुरंत दिक्कत होने लगती है।

इसका कारण यह है कि square की लंबाई और चौड़ाई हमेशा बराबर होती है, जबकि rectangle में ऐसा ज़रूरी नहीं। अगर आप base class में width और height को स्वतंत्र रूप से बदलने की अनुमति देते हैं, और फिर उसी को square में extend करते हैं, तो square की परिभाषा ही गलत हो सकती है — जिससे प्रोग्राम में unintended behavior आने लगेगा।

इसलिए LSP कहता है कि किसी भी derived class को base class की तरह उपयोग करने पर भी व्यवहार में कोई बदलाव नहीं आना चाहिए।

**गलत:**

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
    const area = rectangle.getArea(); // गलत: Returns 25 for Square. Should be 20.
    rectangle.render(area);
  });
}

const rectangles = [new Rectangle(), new Rectangle(), new Square()];
renderLargeRectangles(rectangles);
```

**सही:**

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

### इंटरफेस सेग्रेगेशन प्रिंसिपल (ISP)

JavaScript में interfaces नहीं होते, इसलिए यह principle उतनी सख्ती से लागू नहीं होता जितना कि strongly typed भाषाओं में होता है।
लेकिन फिर भी यह principle JavaScript में महत्वपूर्ण और प्रासंगिक है, क्योंकि JavaScript में भी हम duck typing की वजह से implicit contracts का पालन करते हैं।

ISP कहता है कि "Clients को ऐसे interfaces पर निर्भर नहीं होना चाहिए जिनका वे उपयोग नहीं करते।"

JavaScript में इसका एक अच्छा उदाहरण है — ऐसी classes जो बहुत बड़े settings objects की ज़रूरत रखती हैं।
अगर आप किसी client को ढेर सारे options सेट करने के लिए मजबूर करते हैं, जबकि उसे ज़्यादातर की ज़रूरत नहीं होती,
तो यह एक "fat interface" बन जाता है — जो maintenance और usability दोनों के लिए हानिकारक होता है।

इसलिए:

सभी options को अनिवार्य (mandatory) बनाने की बजाय उन्हें optional बनाएँ।

इससे code modular, simple और client-friendly बनता है।

इस तरह ISP यह सुनिश्चित करता है कि users को सिर्फ वही functionality दी जाए जिसकी उन्हें ज़रूरत है, न कि पूरा भारी interface।

**गलत:**

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
  animationModule() {}, // Most of the time, we won't need to animate when traversing.
  // ...
});
```

**सही:**

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
    animationModule() {},
  },
});
```

### डिपेंडेंसी इनवर्ज़न प्रिंसिपल (Dependency Inversion Principle - DIP)

यह principle दो मुख्य बातें कहता है:

1. High-level modules को low-level modules पर निर्भर नहीं होना चाहिए।
   दोनों को abstractions पर निर्भर होना चाहिए।

2. Abstractions को details पर निर्भर नहीं होना चाहिए, बल्कि details को abstractions पर निर्भर होना चाहिए।

शुरुआत में इसे समझना थोड़ा कठिन लग सकता है, लेकिन अगर आपने AngularJS के साथ काम किया है,
तो आपने इस principle को Dependency Injection (DI) के रूप में देखा होगा।
हालाँकि DIP और DI पूरी तरह एक जैसे नहीं हैं, लेकिन DIP का उद्देश्य यह है कि
high-level modules को low-level modules की details या setup के बारे में पता न हो।

इस तरह के separation से एक बड़ा फ़ायदा यह होता है कि modules के बीच coupling कम हो जाती है।
Coupling एक खराब development practice है क्योंकि यह आपके code को refactor करना मुश्किल बना देती है।

जैसा कि पहले बताया गया, JavaScript में interfaces नहीं होते,
इसलिए abstractions के रूप में implicit contracts होते हैं।
यानि, वो methods और properties जिन्हें एक object या class दूसरे object या class को expose करता है।

नीचे दिए गए उदाहरण में, InventoryTracker किसी भी Request module के साथ काम कर सकता है,
बशर्ते कि उसमें एक requestItems method हो — यही उसका implicit contract है।

इस तरह DIP सुनिश्चित करता है कि high-level logic loosely coupled रहे और आसानी से maintain और extend किया जा सके।

**गलत:**

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

    //गलत हमने एक विशेष request implementation पर निर्भरता (dependency) बना ली है।.
    //हमें requestItems को सिर्फ एक request method: request पर निर्भर होना चाहिए।
    this.requester = new InventoryRequester();
  }

  requestItems() {
    this.items.forEach((item) => {
      this.requester.requestItem(item);
    });
  }
}

const inventoryTracker = new InventoryTracker(["apples", "bananas"]);
inventoryTracker.requestItems();
```

**सही:**

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

// अपनी dependencies को बाहर से तैयार करके और उन्हें inject करके,
// हम आसानी से अपने request module को एक नए, WebSockets का उपयोग करने वाले,शानदार module से बदल सकते हैं।

const inventoryTracker = new InventoryTracker(
  ["apples", "bananas"],
  new InventoryRequesterV2()
);
inventoryTracker.requestItems();
```

## **Testing (परीक्षण)**

Testing shipping से ज़्यादा महत्वपूर्ण है। अगर आपके पास कोई tests नहीं हैं या बहुत कम हैं,
तो हर बार जब आप नया code ship करेंगे, आपको यह भरोसा नहीं होगा कि आपने कुछ तोड़ा नहीं है।
यह तय करना कि "उचित मात्रा में" test क्या होते हैं, आपकी team पर निर्भर करता है —
लेकिन 100% coverage (यानि सभी statements और branches covered हों) रखने से आपको बहुत अधिक भरोसा
और developer शांति मिलती है। इसका मतलब है कि एक बढ़िया testing framework के अलावा,
आपको एक सही coverage tool का भी उपयोग करना चाहिए।

Test न लिखने का कोई बहाना नहीं है। बहुत सारे सही JavaScript test frameworks उपलब्ध हैं,
तो अपनी team की पसंद के अनुसार कोई भी चुन सकते हैं। एक बार जब आपकी team को कोई framework
सही लगे, तो हर नई feature/module के लिए test लिखने की आदत बना लें।

अगर आपकी पसंदीदा approach Test Driven Development (TDD) है, तो बहुत बढ़िया —
लेकिन मुख्य बात यह है कि किसी भी feature को launch करने से पहले या किसी पुराने feature
को refactor करने से पहले coverage goals को ज़रूर पूरा करें।

### एक test में एक ही concept

हर test को केवल एक ही concept पर focus करना चाहिए।
इससे यह तय होता है कि जब test fail हो, तो आपको साफ़-साफ़ पता हो कि क्यों हुआ।
Simple, targeted tests ज़्यादा maintainable होते हैं और debugging को आसान बनाते हैं।

**गलत**

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

**सही:**

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

## **Concurrency (समकालिकता)**

### Callback की जगह Promises का उपयोग करें

Callbacks साफ़ नहीं होते और ये आपके कोड में बहुत ज़्यादा nesting (घोंसले-जैसी संरचना) पैदा कर देते हैं।
ES2015/ES6 से, Promises एक built-in global type के रूप में उपलब्ध हैं।
तो उनका उपयोग करें!

**गलत**

```javascript
import { get } from "request";
import { writeFile } from "fs";

get(
  "https://en.wikipedia.org/wiki/Robert_Cecil_Martin",
  (requestErr, response, body) => {
    if (requestErr) {
      console.error(requestErr);
    } else {
      writeFile("article.html", body, (writeErr) => {
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

**सही:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then((body) => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch((err) => {
    console.error(err);
  });
```

### Async/Await, Promises से भी ज़्यादा साफ़-सुथरे होते हैं

Promises, callbacks की तुलना में एक बहुत साफ़ विकल्प हैं,
लेकिन ES2017/ES8 में आया async और await इससे भी बेहतर समाधान प्रदान करते हैं।

आपको बस अपने function के पहले async keyword लगाना होता है,
और फिर आप imperative तरीके से logic लिख सकते हैं — बिना then() की chain के।

अगर आप ES2017/ES8 की सुविधाओं का उपयोग कर सकते हैं, तो इन्हें ज़रूर अपनाएं!

**गलत**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin")
  .then((body) => {
    return writeFile("article.html", body);
  })
  .then(() => {
    console.log("File written");
  })
  .catch((err) => {
    console.error(err);
  });
```

**सही:**

```javascript
import { get } from "request-promise";
import { writeFile } from "fs-extra";

async function getCleanCodeArticle() {
  try {
    const body = await get("https://en.wikipedia.org/wiki/Robert_Cecil_Martin");
    await writeFile("article.html", body);
    console.log("File written");
  } catch (err) {
    console.error(err);
  }
}

getCleanCodeArticle();
```

## **Error Handling (त्रुटि प्रबंधन)**

Thrown errors एक सही चीज़ हैं!
इनका मतलब है कि runtime ने आपके प्रोग्राम में कुछ गड़बड़ी को पहचान लिया है,
और वह आपको यह बता रहा है —
function execution को वहीं रोककर, Node.js में process को kill करके,
और console में stack trace के ज़रिए आपको notify करके।

### पकड़ी गई (caught) errors को नज़रअंदाज़ ना करें

अगर आपने कोई error catch किया है और फिर उसे ignore कर दिया,
तो इसका मतलब है कि आप ना उसे ठीक कर सकते हैं, ना उस पर प्रतिक्रिया दे सकते हैं।

सिर्फ console.log से error को print कर देना भी बहुत कारगर नहीं होता,
क्योंकि logs में वह message अक्सर खो जाता है।

अगर आपने किसी भी code को try/catch में wrap किया है,
तो इसका मतलब है कि आप मानते हैं वहाँ कोई error आ सकता है —
तो आपको उसके लिए एक प्लान या वैकल्पिक code path तैयार रखना चाहिए।

**गलत**

```javascript
try {
  functionThatMightThrow();
} catch (error) {
  console.log(error);
}
```

**सही:**

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

### Rejected Promises को नजरअंदाज़ ना करें

For the same reason you shouldn't ignore caught errors
from `try/catch`.

**गलत**

```javascript
getdata()
  .then((data) => {
    functionThatMightThrow(data);
  })
  .catch((error) => {
    console.log(error);
  });
```

**सही:**

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

## **Formatting (फ़ॉर्मेटिंग)**

Formatting is subjective. Like many rules herein, there is no hard and fast
rule that you must follow. The main point is DO NOT ARGUE over formatting.
There are [tons of tools](https://standardjs.com/rules.html) to automate this.
Use one! It's a waste of time and money for engineers to argue over formatting.

फ़ॉर्मेटिंग एक subjective (व्यक्तिगत पसंद) चीज़ है। इस गाइड की तरह, इसमें कोई सख़्त और तेज़ नियम नहीं हैं जिन्हें आपको ज़रूर मानना ही है।
मुख्य बात यह है: फ़ॉर्मेटिंग को लेकर बहस मत करो।
इसके लिए बहुत सारे टूल्स उपलब्ध हैं जो इसे ऑटोमैटिकली कर सकते हैं।
ऐसा एक टूल इस्तेमाल करो!
इंजीनियरों का समय और पैसा इस बात पर बहस में बर्बाद करना कि indent कैसा होना चाहिए या quotes कौन से इस्तेमाल करने चाहिए — एक बेवकूफी भरा काम है।

जिन चीज़ों को ऑटोमैटिक फ़ॉर्मेटिंग कवर नहीं कर पाती
(जैसे indentation, tabs vs. spaces, double vs. single quotes, आदि),
उनके लिए नीचे कुछ सुझाव दिए गए हैं।

### Consistent Capitalization का इस्तेमाल करो

JavaScript एक untyped भाषा है, इसलिए **capitalization (अक्षरों का केस)** आपके variables, functions आदि के बारे में बहुत कुछ बता सकती है।
ये नियम पूरी तरह से subjective होते हैं, यानी आपकी टीम जो चाहें नियम बना सकती है।
मुख्य बात यह है:
भले ही आप कोई भी naming convention चुनें, **उसका पालन लगातार (consistently) करें।**
Consistency से ही कोड पढ़ने और समझने में आसानी होती है।

**गलत**

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

**सही:**

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

### फ़ंक्शन कॉल करने वाले और जिनको कॉल किया गया है, वे पास-पास होने चाहिएं

अगर कोई फ़ंक्शन दूसरे फ़ंक्शन को कॉल करता है, तो कोशिश करें कि दोनों फ़ंक्शन source file में एक-दूसरे के करीब हों।
Ideally, कॉल करने वाला फ़ंक्शन उस फ़ंक्शन के ठीक ऊपर हो जिसे वो कॉल कर रहा है।

हम आमतौर पर कोड को ऊपर से नीचे की ओर पढ़ते हैं, ठीक वैसे जैसे हम अख़बार पढ़ते हैं।
इसलिए, अपने कोड को भी वैसे ही लिखें साफ़, समझने योग्य और ऊपर से नीचे तक क्रमबद्ध।

**गलत**

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

**सही:**

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

## **टिप्पणियाँ (Comments)**

### केवल वहीं टिप्पणियाँ करें जहाँ business logic में जटिलता हो।

टिप्पणियाँ (comments) एक तरह से "माफ़ी" होती हैं, ज़रूरी चीज़ नहीं।
सही लिखा गया कोड अक्सर खुद-ब-खुद अपनी व्याख्या कर देता है।

इसका मतलब यह है कि अगर आपका कोड साफ़, स्पष्ट और समझने योग्य है,
तो उसे पढ़ने वाले को यह समझाने के लिए ज़्यादा comments की ज़रूरत नहीं पड़ेगी।

जहाँ बहुत जटिल logic हो, बस वहीं comments का उपयोग करें,
ताकि दूसरे डेवलपर्स उसे जल्दी और सही से समझ सकें।

**गलत**

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

**सही:**

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

### अपने कोडबेस में commented-out कोड न छोड़ें।

Version control किसी वजह से मौजूद है। पुराने कोड को हटाएं और उसे history में रहने दें।

**गलत**

```javascript
doStuff();
// doOtherStuff();
// doSomeMoreStuff();
// doSoMuchStuff();
```

**सही:**

```javascript
doStuff();
```

### Journal comments का इस्तेमाल न करें।

याद रखें, version control का उपयोग करें! Dead code, commented code, और खासकर journal comments की कोई ज़रूरत नहीं होती। अगर आपको किसी बदलाव का इतिहास देखना है, तो git log का उपयोग करें।

**गलत**

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

**सही:**

```javascript
function combine(a, b) {
  return a + b;
}
```

### Positional markers से बचें।

ये ज़्यादातर समय सिर्फ शोर (noise) पैदा करते हैं। आपके कोड की visual structure को समझाने के लिए functions और variable names के साथ सही indentation और formatting ही काफ़ी होती है।

**गलत**

```javascript
////////////////////////////////////////////////////////////////////////////////
// Scope Model Instantiation
////////////////////////////////////////////////////////////////////////////////
$scope.model = {
  menu: "foo",
  nav: "bar",
};

////////////////////////////////////////////////////////////////////////////////
// Action setup
////////////////////////////////////////////////////////////////////////////////
const actions = function () {
  // ...
};
```

**सही:**

```javascript
$scope.model = {
  menu: "foo",
  nav: "bar",
};

const actions = function () {
  // ...
};
```
