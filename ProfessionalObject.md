## Object in ***Professional JavaScript***
### Create Object
#### 工厂模式
```js
function createPerson(name,age,job){
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName =function(){
        console.log(this.name);
    };
    return o;
}

var person1 = createPerson('Greg',27,'Software Engineer');
```
   工厂模式解决了多个相似对象的问题，但却没有解决对象**识别**的问题（检测对象类型  ***instanceof*** ）
#### 构造函数模式
```js
function Person(name,age,job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        console.log(this.name);
    };
}
var person1 = new Preson('Greg', 27, 'Software Engineer');

```
+ 按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。
+ 要创建Person的新实例，必须使用new 操作符。以这种方式调用构造函数实际上会经历以下4个步骤：
   1. 创建一个新对象;
   2. 将构造函数的作用域赋给新对象(因此this指向这个新对象); *在 [you dont'know js](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch1.md) 中 似乎是this绑定了实例 而不是将作用域赋给新对象*;
   3. 执行构造函数中的代码(为这个新对象添加属性);
   4. 返回新对象
 + 在Person 的实例中保存着一个constructor 属性，该属性指向Person
     ```js
     person1.constructor == Person ;  //true
     ```
 + 检测对象类型
   ```js
   person1 instanceof Object ; //true
   person1 instanceof Person ; //true
   ```
#### 构造函数的问题
1. 每个方法都要在每个实例上重新创建一遍
如果构造函数这样定义
```js
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = new Function('console.log(this.name);')//与声明函数在逻辑上是等价的
}
```
可以看出每个Person的实例都包含一个不同的Function实例，以这种方式构建函数，会导致**不同作用的作用域链和标识符解析**，但创建Function新实例的机制仍然是相同的。因此不同实例上的同名函数是不相等的。
```js
var person1 = new Preson('Greg', 27, 'Software Engineer');
var person2 = new Preson('Nicholas', 29, 'Doctor');
preson1.sayName ==person2.sayName; //false
```
2. 通过把函数定义转移到构造函数外来解决这个问题
```js
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = sayName;
}

function sayName() {
    console.log(this.name);
}
var person1 = new Preson('Greg', 27, 'Software Engineer');
var person2 = new Preson('Nicholas', 29, 'Doctor');
```
3. 产生新问题：
   * 虽然preson1与preson2对象共享了在全局函数作用域中定义的同一个sayNam()函数，的确解决了两个函数做同一件事的问题。
   * 在全局作用域中定义的函数实际上只能被某个对象调用，这让全局作用域有点名不其实。**而且**如果对象需要定义很多方法，那么就要定义很多个全局函数，于是我们这个自定义的引用类型就丝毫没有封装性可言。    
#### 原型模式
**prototype**属性是一个指针，指向一个*对象*，这个*对象*的用途是包含可以有特定类型的所有实例共享的属性和方法。使用原型对象的**好处**是可以让所有对象实例共享***它***所包含的属性和方法
```js
function Person() {

}
Person.prototype.name = 'Nicholas';
Person.prototype.age = 29;
Person.prototype.job = 'Software Engineer';
Person.prototype.sayName = function() {
    console.log(this.name);
};
var person1 = new Person();
person1.sayName(); //'Nicholas'
var person2 = new Person();
person2.sayName(); //'Nicholas'
```
+ 无论什么时候，只要创建了一个新函数，就会根据一组特定的规则为该函数创建一个*prototype属性*，这个属性*指向函数的原型对象*。在默认的情况下，所有原型对象都会自动获得一个*constructor*属性，这个属性是一个指向prototypr属性所在函数的指针。Person.prototype.constructor 指向 Person。
+ Firefox、Safari和Chrome在每个对象上都支持一个属性*__proto__*
```js
Person.prototype.isPrototypeOf(person1);//true
Person.prototype.isPrototypeOf(person2);//true

Object.getPrototypeOf(person1)==Person.prototype;//true
Object.getPrototypeOf(person1).name;//"Nicholas"
```
+ 代码读取某个对象的某个属性时，会依次从*对象实例本身*，*原型对象*，*原型对象的原型对象*查找
+ 如果在实例中添加了一个属性，这个属性与对象的某个属性同名，该属性将*屏蔽*原型中的那个同名属性。
```js
function Person() {

}
Person.prototype.name = 'Nicholas';
Person.prototype.age = 29;
Person.prototype.job = 'Software Engineer';
Person.prototype.sayName = function() {
    console.log(this.name);
};
var person1 = new Person();
var person2 = new Person();
person1.name = 'Greg';
console.log(person1.name); //'Greg'---来自实例
console.log(person2.name);//'Nicholas'---来自原型
```
+ 使用*hasOwnProperty()*方法可以检测一个属性是存在于实例中，还是存在于原型中。
+ delete 操作符
```js
function Person() {

}
Person.prototype.name = 'Nicholas';
Person.prototype.age = 29;
Person.prototype.job = 'Software Engineer';
Person.prototype.sayName = function() {
    console.log(this.name);
};
var person1 = new Person();
var person2 = new Person();
console.log(person1.hasOwnProperty('name')); // false

person1.name = 'Greg';
console.log(person1.name); //'Greg'-------来自实例
console.log(person1.hasOwnProperty('name')); // true

console.log(person2.name); //'Nicholas'---来自原型
console.log(person2.hasOwnProperty('name')); // false

delete person1.name;
console.log(person1.name); //'Greg'------来自原型
console.log(person1.hasOwnProperty('name')); // false
```
+ 原型与in操作符
在单独使用*in*操作符时，*in*操作符会在通过对象能够访问给定属性是返回true，无论该属性存在于实例中还是原型中。
```js
function Person() {

}
Person.prototype.name = 'Nicholas';
Person.prototype.age = 29;
Person.prototype.job = 'Software Engineer';
Person.prototype.sayName = function() {
    console.log(this.name);
};
var person1 = new Person();
var person2 = new Person();
console.log('name' in person1); // true

person1.name = 'Greg';
console.log(person1.name); //'Greg'-------来自实例
console.log('name' in person1); // true


console.log(person2.name); //'Nicholas'---来自原型
console.log(person2.hasOwnProperty('name')); // false
console.log('name' in person1); // true

delete person1.name;
console.log(person1.name); //'Greg'------来自原型
console.log(person1.hasOwnProperty('name')); // false
console.log('name' in person1); // true
```
+ 只要*in*操作符返回true而*hasOwnProperty()*返回false,就可以确定属性是原型中的属性。
+ 更简单的原型语法
```js
Person.prototype = {
    name: 'Nicholas',
    age: 29,
    job: 'Software Engineer',
    sayName: function() {
        console.log(this.name);
    }
};
```
+ 重设*constructor*属性
```js
function Person() {

}
Person.prototype = {
    constructor:Person,
    name: 'Nicholas',
    age: 29,
    job: 'Software Engineer',
    sayName: function() {
        console.log(this.name);
    }
};
```
重设*constructor*属性会导致*constructor*的**Enumerable**特性被设置为true，而原生的*constructor*属性是不可枚举的。
使用*Object.defineProperty()*
```js
function Person() {

}
Person.prototype = {
    name: 'Nicholas',
    age: 29,
    job: 'Software Engineer',
    sayName: function() {
        console.log(this.name);
    }
};
//ECMAScript5
Object.defineProperty(Person.prototype, 'constructor', {
    enumerable: false,
    value: Person
});
```
+ 原型的动态性---原型的创建顺序会影响执行
```js
function Person(){

}
var friend = new Person();

Person.prototype.sayHi = function(){
    console.log('Hi');
}
friend.sayHi(); //'Hi
```
重写整个原型对象时，情况发生变化
```js
function Person() {

}
var friend = new Person();
Person.prototype = {
    name: 'Nicholas',
    age: 29,
    job: 'Software Engineer',
    sayName: function() {
        console.log(this.name);
    }
};

friend.sayName(); //'error in nodejs :friend.sayName is not a function'
```
重写原型对象切断了现有原型与任何之前已经存在的对象实例之间的联系；*它们*(指之前存在的对象实例)的引用仍然是最初的原型。
#### 组合使用构造函数模式和原型模式 ***使用最广泛，认同度最高***
```js
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    this.friends = ['Shelby', 'Court']
}
Person.prototype = {
    constructor: Person,
    sayName: function() {
        console.log(this.name);
    }
};
var person1 = new Person('Greg', 27, 'Software Engineer');
var person2 = new Person('Nicholas', 29, 'Doctor');
person1.friends.push('Van');
console.log(person1.friends); //'Shelby,Count,Van'
console.log(person2.friends); //'Shelby,Count'
console.log(person1.friends === person2.friends); //false
console.log(person1.sayName === person2.sayName); //true
```
#### 动态原型模式
```js
function Person(name, age, job) {
    this.name = name;
    this.age = age;
    this.job = job;
    if (typeof this.sayName != 'function') {
        Person.prototype.sayName = function() {
            console.log(this.name);
        }
    }
}
var friend = new Person('Nicholas', 29, 'Software Engineer');
friend.sayName();
```
只有在*sayName()*方法不存在的情况下，才会将它添加到原型中。因为第一次调用后*Person*已经拥有了*sayName()*所以，*if()*为false ，*if()*中的代码不会再执行。
#### 寄生构造函数模式 HybridRactoryPattern
+ 返回的对象与构造函数或者与构造函数的原型属性之间没有关系；不能依赖*instanceof*操作符来确定对象类型。
```js
function Person(name, age, job) {
    var o = new Object();
    o.name = name;
    o.age = age;
    o.job = job;
    o.sayName = function() {
        console.log(this.name);
    };
    return o;
}
var friend = new Person('Nicholas', 29, 'Software Engineer');
friend.sayName();
```
+ 创建一个具有额外方法的特殊数组
```js
function SpecialArray() {
    var values = new Array();
    values.push.apply(values, arguments)
    values.toPipedString = function() {
        return this.join('|');
    }
    return values;
}
var colors = new SpecialArray('red', 'blue', 'green');
console.log(colors.toPipedString()); //'red|blue|green'
```
#### 稳妥构造函数模式 
+ durable objects(稳妥对象) 没有公共属性，其方法也不引用this的对象。
+ 与寄生构造函数的不同
   + 新创建对象的实例方法不引用this
   + 不使用new 操作符调用构造函数
```js
function Person(name, age, job) {
    var o = new Object();
   //可以在这李定义私有变量和函数
   //添加方法
    o.sayName = function() {
        console.log(name);
    };
    返回对象
    return o;
}
var friend = new Person('Nicholas', 29, 'Software Engineer');
friend.sayName();
