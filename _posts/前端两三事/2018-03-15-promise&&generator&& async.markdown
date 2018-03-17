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

Promise的写法只是回调函数的改进，使用then之后，异步的执行更为清楚。但是一眼看过去也是一堆的then，还是没法得到好的改进。

Promise对象的缺点：

无法取消Promise，一旦新建它就会立即执行，无法中途取消。


### 二、Generator

可以让你”暂停“一个函数（而不暂停整个程序），它也能你从上至下写异步函数。

Generator的声明方式类似一般的函数声明，多了个*号，并且一般可以在函数内看到yield

```javascript
    function* generator() {
        yield 'a';
        yield 'b';
        yield 'c';
    }
    var result = generator()
    result.next()//{value:'a',done:false}
    result.next()//{value:'b',done:false}
    result.next()//{value:'c',done:false}
    result.next()//{value:undefined,done:true}
```

yield和yield* 只能在generator函数内部使用，一般的函数内使用会报错

```javascript
    function generator() {
        yield* 'a';
        yield* 'b';
        yield* 'c';
    }
    var result = generator()
    result.next()//Uncaught ReferenceError: yield is not defined
```

yield*后面接受一个iterable object为参数，然后去迭代这个迭代器

```javascript
    function* yiled1(){
        yield 'hello';
    }
    var result1 = yiled1()//该函数并不执行，返回的也不是函数的执行结果，而是内部状态的指针对象，迭代器，必须使用next方法，使得指针指向下一个状态
    result1.next()// {value:'hello',done:false}

    function* yiled2(){
        yield* 'hello';
    }
    var result2 = yiled2()
    result2.next()// {value:'h',done:false}

    function* yiled3(){
        yield helloFun();
    }
    function* helloFun(){
        return 'hello'
    }
    var result3 = yiled3()
    result3.next()// {value:helloFun,done:false}
```




分段执行

### 三、Async/Await

ES7的特性，是生成器和promise更高级的封装。

async是“异步”的简写。async/await 理解为先申明是一个async异步函数，await则是等待这个异步执行完成。

async返回的是一个promise对象，看以下代码栗子

```javascript
    var hello =  function(){
        return new Promise(function(resolve,reject){
            resolve('hello world')
        })
    }

    var test = async function(){
        var result = await hello()//await只能用在async函数里
        console.log('result:',result)
    }
    test()

```

对比promise，不用再写then和catch，那是如何捕获错误的？

```javascript
    var hello =  function(){
        return new Promise(function(resolve,reject){
            resolve('hello world')
        })
    }

    var test = async function(){
        try{
            ...
        }catch(err){
            ...
        }
    }
    test()
```
用try和catch捕获错误

多个await的写法
```javascript
   var fun1 = function(){
        return new Promise(function(resolve,reject){
            if(2>1){ 
				resolve('result1')
			}else{
				reject('fun1-err')
			}
        })
    }
    var fun2 = function(){
        return new Promise(function(resolve,reject){
            if(2<1){ 
				resolve('result1')
			}else{
				reject('fun2-err')
			}
        })
    }
    var array = [fun1,fun2]
    var test = async function(){
		try{
			for(let f of array){
                console.log('成功获得结果',await f())
            }

		}catch(err){
			console.log('失败的结果',err)
		}
        
    }
	await test()
    //成功获得结果 result1
    //失败的结果 fun2-err
```
