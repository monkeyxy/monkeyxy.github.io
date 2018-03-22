---
title: JavaScript的面向编程问题
date: '2018-03-17 15:30:00'
categories: [
Front
]
layout: post
author: xy
---

**1、what 面向对象**

面向对象的语言的特征就是基于类的概念。基于类可以创建任意多个实例对象


```javascript
function People(name){//构造函数
	this.name = name;
	this.say = function(){
		console.log('say',this.name)
	}
}
var me = new People('xy')
console.log(typeof People)//function  js中是没有函数这个类型的，函数是一种特殊对象的存在，因此函数的原型是对象
console.log(me.prototype)//undefined 被new出来的普通对象是没有原型的
console.log(me.__proto__)// {constructor: ƒ}
console.log(People.prototype)//object  原型也是对象
console.log(People.prototype.constructor)//function
```

![原型链的关系](/images/js/面向对象1.jpg)

new People中做了哪些事？(往下看，构造函数一块有说哒~)



**2、面向对象特点**

封装:对象内部的变化对外界是不可见的。一个对象它所封装的是自己的方法和属性，不需要依赖其他对象完成的操作。

继承:可以解决代码复用问题，所有子类中重复的属性和方法，可以抽离出来，在父类中定义这些相同的属性和方法，所有的子类不需要定义这些属性和方法，只需要通过继承父类中的属性和方法。

多态:可以理解为子类在继承父类方法的时候，通过传参不同，实现多态。


**3、创建对象的三种方式（工厂模式，构造函数，原型模式）**

a.工厂模式，使用简单的函数创建对象，为对象添加属性和方法，然后返回对象

```javascript
function createStudent(name,age){
	var obj = new Object()
	obj.name = name;
	obj.age = age;
	obj.say = function(){
		console.log('my name is'+this.name)
	}
	return obj
}
var student = createStudent('xy',18)
console.log(student)//{name: "xy", age: 18, say: ƒ}
console.log(student instanceof createStudent)//false
```
createStudent()方法可以重复调用，能够接受参数来构建一个对象，工厂模式解决了创建多个类似对象问题，但是却没法解决对象识别问题。再次进化新的模式——构造函数


b.构造函数模式，创建自定义引用类型，可以像创建内置对象实例一样使用new操作符，这种方法的缺点是，构造函数的每个成员都无法复用，每次创建出的对象都只有私有变量和私有方法，不能实现共用

```javascript
function CreateStudent(name,age){
	this.name = name;
	this.age = age;
	this.say = function(){
		console.log('my name is'+this.name)
	}
}
var student = new CreateStudent('xy',18)
console.log(student)//CreateStudent {name: "xy", age: 18, say: ƒ}
console.log(student instanceof CreateStudent)//true
```
与工厂模式对比：

1、构造函数的函数名首字母大写

2、没有用return

3、使用了new操作符

其中的new做了几件事。

```javascript
var obj = new Object;//创建一个空对象
obj.__proto__ = CreateStudent.prototype;//将这个空对象的__proto__指向构造函数的prototype
student.call(obj)//将构造函数的作用域赋给新对象，因此People中的this指向obj，然后再调用People函数
return obj;//第三步执行函数内的语句之后，返回一个新的对象obj
```

c.原型模式，使用构造函数的prototype属性来指定共享的属性和方法，即使用构造函数定义实例属性，使用原型定义共享的属性和方法


