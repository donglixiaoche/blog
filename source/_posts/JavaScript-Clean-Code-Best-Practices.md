---
title: JavaScript Clean Code - Best Practices
date: 2019-05-27 15:48:50
categories: 翻译
tags: 
- 翻译
- JavaScript
---

> 本文是一篇翻译过来的文章，原文地址：[https://devinduct.com/blogpost/22/javascript-clean-code-best-practices](https://devinduct.com/blogpost/22/javascript-clean-code-best-practices)

## 介绍

如果你关心代码本身以及代码是如何被编写的，而不是仅仅关心代码是否有效，你可以说你练习并且关心整洁的代码。一个专业的开发人员会为未来的自己以及其他人员编写代码，而不仅仅是为了机器。你所编写的任何代码都不会只被编写一次，相反，它会静静地等待下一个接手的人，并且让他头大。希望那个人不是你~

基于此，整洁的代码可以被定义为具有以下特征的代码：良好的可读性，便于被人所理解，易于修改和扩展。

<!-- more -->

问问你自己，有多少次你接手别人的工作时你的第一印象时下面的**_"WTF"_** 问题之一：

**_"WTF is that?"_**
**_"WTF did you do here?"_**
**_"WTF is this for?"_**

下面这张图片很好地展示了上述内容：
![WTF image](https://camo.githubusercontent.com/2050cd696ecddcabad1380b1964c48a60597323e/687474703a2f2f7777772e6f736e6577732e636f6d2f696d616765732f636f6d6963732f7774666d2e6a7067)

**_Robert C. Martin(Uncle Bob)_**的一句话应该会让你思考你写代码的方式：
> 虽然糟糕的代码也可以运行，但是如果代码不整洁，它会让开发组织陷入困境。
>(Even bad code can function. But if the code isn’t clean, it can bring a development organization to its knees.)

在这边文章中将会把注意力放在JavaScript上，但是文章中所说的一些原则也可以被应用在其他编程语言中。

## 你真正来这阅读的部分-整洁代码 最佳实践

### 强类型检查

使用 === 代替 ==
```javascript
// If not handled properly, it can dramatically affect the program logic. It's like, you expect to go left, but for some reason, you go right.
0 == false // true
0 === false // false
2 == "2" // true
2 === "2" // false

// example
const value = "500";
if (value === 500) {
  console.log(value);
  // it will not be reached
}

if (value === "500") {
  console.log(value);
  // it will be reached
}
```

### 变量

变量名应该能够解释变量背后的意图和作用，这样会使变量变得易于搜索和理解。

坏代码：
```javascript
let daysSLV = 10;
let y = new Date().getFullYear();

let ok;
if (user.age > 30) {
  ok = true;
}
```

好代码：

```javascript
const MAX_AGE = 30;
let daysSinceLastVisit = 10;
let currentYear = new Date().getFullYear();

...

const isUserOlderThanAllowed = user.age > MAX_AGE;
```

不要在变量名中添加额外的不必要的单词

坏代码：
```javascript
let nameValue;
let theProduct;
```

好代码：
```javascript
let name;
let product;
```

不要让变量的含义依附上下文

坏代码：
```javascript
const users = ["John", "Marco", "Peter"];
users.forEach(u => {
  doSomething();
  doSomethingElse();
  // ...
  // ...
  // ...
  // ...
  // Here we have the WTF situation: WTF is `u` for?
  register(u);
});
```

好代码：
```javascript
const users = ["John", "Marco", "Peter"];
users.forEach(user => {
  doSomething();
  doSomethingElse();
  // ...
  // ...
  // ...
  // ...
  register(user);
});
```

不要添加不必要的上下文

坏代码：
```javascript
const user = {
  userName: "John",
  userSurname: "Doe",
  userAge: "28"
};

...

user.userName;
```

好代码：
```javascript
const user = {
  name: "John",
  surname: "Doe",
  age: "28"
};

...

user.name;
```

### 函数

使用长的具有描述性的名字，考虑将函数名定义成一种行为。函数名应该是一个动词或者短语，并且完全暴露函数功能和变量名的作用。

坏代码：

```javascript
function notif(user) {
  // implementation
}
```

```javascript
function notifyUser(emailAddress) {
  // implementation
}
```

避免使用过多的参数。理想的情况下，一个函数的变量不应该超过两个。函数的变量越少越容易进行测试。

坏代码：

```javascript
function getUsers(fields, fromDate, toDate) {
  // implementation
}
```

好代码：
```javascript
function getUsers({ fields, fromDate, toDate }) {
  // implementation
}

getUsers({
  fields: ['name', 'surname', 'email'],
  fromDate: '2019-01-01',
  toDate: '2019-01-18'
});
```

使用默认参数值代替条件语句

坏代码：
```javascript
function createShape(type) {
  const shapeType = type || "cube";
  // ...
}
```

好代码：
```javascript
function createShape(type = "cube") {
  // ...
}
```

每个函数应该只做一件事情（SRP 单一职责原则）。避免在一个函数中执行过多的动作。

坏代码：
```javascript
function notifyUsers(users) {
  users.forEach(user => {
    const userRecord = database.lookup(user);
    if (userRecord.isVerified()) {
      notify(user);
    }
  });
}
```

好代码：
```javascript
function notifyVerifiedUsers(users) {
  users.filter(isUserVerified).forEach(notify);
}

function isUserVerified(user) {
  const userRecord = database.lookup(user);
  return userRecord.isVerified();
}
```

使用`Object.assign`设置默认对象属性

坏代码：
```javascript
const shapeConfig = {
  type: "cube",
  width: 200,
  height: null
};

function createShape(config) {
  config.type = config.type || "cube";
  config.width = config.width || 250;
  config.height = config.width || 250;
}

createShape(shapeConfig);
```

好代码：
```javascript
const shapeConfig = {
  type: "cube",
  width: 200
  // Exclude the 'height' key
};

function createShape(config) {
  config = Object.assign(
    {
      type: "cube",
      width: 250,
      height: 250
    },
    config
  );

  ...
}

createShape(shapeConfig);
```

不要在参数中设置flag，应该这些flag表明函数在做额外的操作。

坏代码：
```javascript
function createFile(name, isPublic) {
  if (isPublic) {
    fs.create(`./public/${name}`);
  } else {
    fs.create(name);
  }
}
```

好代码：
```javascript
function createFile(name) {
  fs.create(name);
}

function createPublicFile(name) {
  createFile(`./public/${name}`);
}
```

不要污染默认的全局对象，如果你需要扩展一个已经存在的对象，使用ES6中的类和继承，不要直接修改原生对象的原型链

坏代码：
```javascript
Array.prototype.myFunc = function myFunc() {
  // implementation
};
```

好代码：
```javascript
class SuperArray extends Array {
  myFunc() {
    // implementation
  }
}
```

### 条件语句

避免否定的条件语句

坏代码：
```javascript
function isUserNotBlocked(user) {
  // implementation
}

if (!isUserNotBlocked(user)) {
  // implementation
}
```

好代码：
```javascript
function isUserBlocked(user) {
  // implementation
}

if (isUserBlocked(user)) {
  // implementation
}
```

当确定值为Boolean而不是undefined或者null时，使用短的条件语句。这可能是很小的细节，但是却值得一提。

坏代码：
```javascript
if (isValid === true) {
  // do something...
}

if (isValid === false) {
  // do something...
}
```

好代码：
```javascript
if (isValid) {
  // do something...
}

if (!isValid) {
  // do something...
}
```

尽可能避免条件语句，使用继承和多态来代替

坏代码：
```javascript
class Car {
  // ...
  getMaximumSpeed() {
    switch (this.type) {
      case "Ford":
        return this.someFactor() + this.anotherFactor();
      case "Mazda":
        return this.someFactor();
      case "McLaren":
        return this.someFactor() - this.anotherFactor();
    }
  }
}
```

好代码：
```javascript
class Car {
  // ...
}

class Ford extends Car {
  // ...
  getMaximumSpeed() {
    return this.someFactor() + this.anotherFactor();
  }
}

class Mazda extends Car {
  // ...
  getMaximumSpeed() {
    return this.someFactor();
  }
}

class McLaren extends Car {
  // ...
  getMaximumSpeed() {
    return this.someFactor() - this.anotherFactor();
  }
}
```

### ES 类

Class是JavaScript中的语法糖，使用的也是原型继承。在代码中你应该使用ES6中的Class语法糖代码原生构造函数。


坏代码：
```javascript
const Person = function(name) {
  if (!(this instanceof Person)) {
    throw new Error("Instantiate Person with `new` keyword");
  }

  this.name = name;
};

Person.prototype.sayHello = function sayHello() { /**/ };

const Student = function(name, school) {
  if (!(this instanceof Student)) {
    throw new Error("Instantiate Student with `new` keyword");
  }

  Person.call(this, name);
  this.school = school;
};

Student.prototype = Object.create(Person.prototype);
Student.prototype.constructor = Student;
Student.prototype.printSchoolName = function printSchoolName() { /**/ };
```

好代码：
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  sayHello() {
    /* ... */
  }
}

class Student extends Person {
  constructor(name, school) {
    super(name);
    this.school = school;
  }

  printSchoolName() {
    /* ... */
  }
}
```

使用方法链。包括JQuery和Lodash在内的很多包都使用了这种模式。这样做的好处是，你的代码会看起来更加简洁。在你的类中，简单地在方法中返回`this`关键字，你就可以将更多地类方法串接起来。

坏代码：
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  setSurname(surname) {
    this.surname = surname;
  }

  setAge(age) {
    this.age = age;
  }

  save() {
    console.log(this.name, this.surname, this.age);
  }
}

const person = new Person("John");
person.setSurname("Doe");
person.setAge(29);
person.save();
```

好代码：
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }

  setSurname(surname) {
    this.surname = surname;
    // Return this for chaining
    return this;
  }

  setAge(age) {
    this.age = age;
    // Return this for chaining
    return this;
  }

  save() {
    console.log(this.name, this.surname, this.age);
    // Return this for chaining
    return this;
  }
}

const person = new Person("John")
    .setSurname("Doe")
    .setAge(29)
    .save();
```

### 普遍的需要注意和避免的地方

一般来说，你应该尽可能地避免编写重复的代码，以及在代码中留下用不到的死代码。

你可能会因为各种原因在代码中编写冗余的代码。比如说，你有两个很相似的对象，彼此间只有一点点的不同。但是由于他们的不同点的本质，或者紧迫的项目排期，你不得不创建两个几乎拥有相同的代码的类或者函数。而想要去除这些冗余的代码意味着抽象出不同点，在更高的层次上来执行操作。

关于死代码，指的是那些再也用不到的代码，可能在项目开发的某个节点，你的一些决定让这些代码不再包含任何的用途。你应该在代码库中搜索并删除所有用不到的函数和代码块。我的一个建议是，一旦你决定某些代码不再被需要，马上删除它。否则你可能会忘记的~

下面这张图片表明了你看到一些死代码但是又不记得是否有作用时的感受：
[when you forget the purpose of dead code](https://pics.me.me/sometimes-my-code-dont-know-what-it-does-but-i-49866360.png)

### 总结
上面所描述的只是你能的优化代码的一小部分。这里所描述的原则，是我个人看来人们经常不遵守的原则。人们尝试着去遵守这些原则，但是往往由于各种原因导致失败。可能在项目开始的时候，代码很简洁、整齐，但是当排期越来越近时，这些原则经常都会被忽略并且被移到**_"TODO"_**或者**_REFACTOR_**的部分。在这些时候，客户更关心的是排期的截止，而不是编写更加简洁的代码。
