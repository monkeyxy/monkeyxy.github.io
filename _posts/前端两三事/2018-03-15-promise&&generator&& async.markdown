---
title: promise,generator,async await
date: '2018-03-05 22:00:00'
categories: [
Front
]
layout: post
author: xy
---

### 一、Promise


在promise没出现之前，我们经常是通过回调函数来解决异步的问题

栗子：

```javascript
    fun1(function(res1){
        fun2(function(res2){
            fun3(function(res2){
                ...
            })
        })
    })
    
```

函数的执行顺序是fun1,fun2,fun3,由外向内执行，达到了某种意义上的顺序执行，每一层回调函数都需要依赖上一层的回调执行完，所以形成了层层嵌套的回调地狱。但是在代码上却增加了阅读难度，可读性大大降低。


![promise对象](/images/js/promise.jpg)

Promise是一个函数，这个函数上包含了我们经常使用的方法,reject,resolve,then,catch等等


先上一段常见的promise写法

```javascript
    new Promise(param).then(function(res){
        console.log('成功的回调',res)
    }).catch(function(err){
        console.log('失败的回调',err)
    })
```
可见promise最大的好处是在异步的过程中，执行代码和处理结果的代码清晰分离。

Promise传入两个参数resolve,reject。当异步操作成功后，Promise会返回一个值，这个状态为resolved；当异步操作失败后，Promise会抛出一个错误，这个状态为rejected

```javascript
    function getData(){
        return new Promise(function(resolve,reject){
            if(...){
                resolve('something')
            }else{
                reject('error')
            }
        })
    }
    const obj={'num':1}//参数
    getData(obj).then(function(res){
        console.log('resolve返回的内容',res)//res: something
    }).catch(function(err){
        console.log('reject返回的内容',err)//err: error
    })
```

Promise.all:接收的是promise数组，接收的promise数组同时请求，等待结果返回。

```javascript
    var promise1 = new Promise(function(resolve,reject){
        //doing something
    })
    var promise2 = new Promise(function(resolve,reject){
        //doing something
    })

    var all = Promise.all([promise1,promise2]).then(function(){
        //等promise1和promise2执行结果返回之后，再执行all
    })
    
```

返回最开始的那个回调地狱的例子，那么用promise写的话又是如何的呢？

```javascript
    new Promise(function(res,rej){
        //
    }).then(function(){
        return new Promise(function(res,rej){
            //
        })
    }).then(function(){
         return new Promise(function(res,rej){
            //
        })
    }).catch(function(){

    })

```

then方法可以返回一个promise。

以上的写法仍处于混乱，可以抽离成以下写法

```javascript
    function task1(){
        return new Promise(function(res,rej){
            //doing something
        })
    }
     function task2(){
        return new Promise(function(res,rej){
            //doing something
        })
    }
     function task3(){
        return new Promise(function(res,rej){
            //doing something
        })
    }
    task1()
        .then(task2())
        .then(task3())
        .then(function(){
            //....task1 2 3结果拿到后的处理
        })
```



Promise对象的缺点：
1、无法取消Promise，一旦新建它就会立即执行，无法中途取消。

2、如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。

3、当处于Pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

### 二、Generator

可以让你”暂停“一个函数（而不暂停整个程序），它也能你从上至下写异步函数。

Generator的声明方式类似一般的函数声明，多了个*号，并且一般可以在函数内看到yield

```javascript
    function* generator() {
        yield 'a';
        yield 'b';
        yield 'c';
    }
```

分段执行

### 三、Async/Await

ES7的特性，是生成器和promise更高级的封装

