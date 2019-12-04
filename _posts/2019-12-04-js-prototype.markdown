---
layout: post
title:  "Javascript Prototype"
date:   2019-12-04 07:07:01 +0800
categories: javascript
---

# Javascript 原型

## 什么是原型

每个对象<sup>注1</sup>都有一个`原型`，原型是另一个对象。当访问一个对象的某个属性时，先在对象本身查找，如果没有找到该属性，就在它的原型上查找，如果仍没找到，就在它原型的原型上查找，以此类推。这种原型的原型组成的链条，就是**原型链**。

`原型`实际上是一个对象，所以严格来说应该叫`原型对象`。但因为习惯，大家都称之为“原型”，下面的内容同样如此。

对一般方式创建的对象（比如`const obj = {}`），它的原型是`Object.prototype`，`Object.prototype`这个对象没有原型。

**注意**，对象的`prototype`属性的值并不表示它的原型<sup>注2</sup>。

在JS中函数也是对象，所以函数也有原型。一般方式创建的函数（比如`function f() {}`）的原型是`Function.prototype`。

Object、Function、RegExp、Date、Number等是内置的constructor，它们的原型是同一个。因为可以通过`__proto__`来获取对象的原型，所以：

```javascript
Object.__proto__ ===
  Function.__proto__ ===
  RegExp.__proto__ ===
  Date.__proto__ ===
  Number.__proto__
// 以上忽略 === 的结合性
```

`Function`的原型**等于**它的`prototype`属性值，但对`Object、RegExp、Date、Number`等，它们的原型**并不等于**它们的`prototype`属性，这一点`Function`比较特殊。

综上所以有：

```javascript
Object.prototype.__proto__ === null

obj.__proto__ === Object.prototype

f.__proto__ === Function.prototype

f.__proto__.__proto__ === Function.prototype.__proto__ === Object.prototype

Function.__proto__.__proto__ ===
  Date.__proto__.__proto__ ===
  Object.__proto__.__proto__ ===
  Object.prototype

Function.__proto__ === Function.prototype
Date.__proto__ !== Date.prototype
RegExp.__proto__ !== RegExp.prototype

// 以上忽略 === 的结合性
```

* 注1：`Object.prototype`以及用`Object.create(null)`创建的对象就没有原型
* 注2：`Function.prototype`等于Function的原型


## 关于function

如果有函数someFunction，则它自带一个prototype属性，且prototype的constructor属性等于someFunction自己。即：

```javascript
function someFunction() {}
someFunction.prototype.constructor === someFunction
```


## 关于`prototype`属性

如果通过`const obj = new SomeFunction()`的方式创建一个对象，那么`obj`的原型则等于`SomeFunction.prototpye`。即：

```javascript
const obj = new SomeFunction();
obj.__proto__ === SomeFunction.prototpye;
```

## 关于`Object.create`

```javascript
const obj = Object.create(another);
obj.__proto__ === another;
```

## 关于`__proto__`

It's not the standard of ECMAScript, but has become the de facto standard of the industry.

## 关于`class`

如果有constructor A，那么`class B extends A`的结果是：

- B的原型是A，即`B.__proto__ === A`
- B是一个新的constructor，也自带一个prototype属性，且`B.prototype.constructor === B`，与定义一个function同理
- `new B().__proto__ === B.prototype`，与new一个function同理

## 关于原型链上属性的读写

当访问对象的某属性值时，会从它的原理链上逐级查找并返回。但在写属性值时，此值只会写在对象自己身上，不会影响原型：

```javascript
const a = {pa: 123};
const b = Object.create(a);
b.pa === 123; // 属性值取自原型
b.pb = 456;
a.pb === undefined; // 原型不受影响
```

## 关于继承

根据JS的原型的特性，可以实现类似Java一样的继承。
