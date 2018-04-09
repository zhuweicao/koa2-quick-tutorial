# Promise 语法

## 同步与异步
> 我们知道，JavaScript的执行环境是「单线程」。 
所谓单线程，是指JS引擎中负责解释和执行JavaScript代码的线程只有一个，也就是一次只能完成一项任务，这个任务执行完后才能执行下一个，它会「阻塞」其他任务。这个任务可称为主线程。 
但实际上还有其他线程，如事件触发线程、ajax请求线程等。这也就引发了同步和异步的问题。

> 假如你要做两件事，烧水、刷牙
同步：你烧水，等水烧开了你再去刷牙
异步：你烧水，不等水烧开就去刷牙了，水烧开了会发出声音告诉你（callback），然后你再处理水烧开之后的事情

## 回调函数
> 回调函数是一段可执行的代码段，它以「参数」的形式传递给其他代码，在其合适的时间执行这段（回调函数）的代码。

## 异步问题引发问题
* 异步代码实现网络请求
    ```
    ajax('url1',function(data1) {
        ajax('url2',function(data2){
            ajax('url3',function(data3){
                ajax('url4',function(data4){
            
                })
            })
        })
    })
    ```

* Promise 实现异步网络请求
    ```
    ajax('url1')
    .then(function(data1){
        return ajax('url2')
    })
    .then(function(data2){
        return ajax('url3')
    })
    .then(function(data3){
        
    })
    ```

## Promise 介绍
以下是MDN对Promise的定义
> The Promise object is used for asynchronous computations. A Promise represents a single asynchronous operation that hasn't completed yet, but is expected in the future.
译文：Promise对象用于异步操作，它表示一个尚未完成且预计在未来完成的异步操作。

#### Promise 作用
> 解决异步嵌套问题

#### 支持情况
> ES2015正式发布（也就是ES6，ES6是它的乳名），其中Promise被列为正式规范。作为ES6中最重要的特性之一，我们有必要掌握并理解透彻。本文将由浅到深，讲解Promise的基本概念与使用方法。

#### 基本用法
> 使用`new` 来构建一个`Promise`。`Promise` 接受一个「函数」作为参数，该函数的两个参数分别是 `resolve` 和 `reject` 。这两个函数就是就是「回调函数」，由JavaScript引擎提供。

```
var promise = new Promise(function (resolve, reject) {
    if (/* 异步操作成功 */) {
        resolve(data);
    } else {
        /* 异步操作失败 */
        reject(error);
    }
});
```

> `resolve` 函数的作用：在异步操作成功时调用，并将异步操作的结果，作为参数传递出去。

> `reject` 函数的作用：在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

#### 基本 API
##### .then()

> 语法：Promise.prototype.then(onResolve, onRejected)
```
    new Promise(function (resolve, reject) {
        if (/* 异步操作成功 */) {
            resolve(data);
        } else {
            /* 异步操作失败 */
            reject(error);
        }
    })
    .then(function(data){
        // data 接收参数
        // 操作成功处理
        return data
    },function(error){
        // error 接收参数
        // 操作失败处理
    })
    .then(function(data) {
        // data 接收参数
        // 也可以返回一个新的 Promise
        return new Promise((resolve,reject)=>{
            resolve("newData")
        })
    })
    .then(function(newData){
        // 处理结果
    })
```

> 作用：当 Promise 对象内部调用 `resolve` 方法会触发 `.then()` 继续执行代码

##### .catch()
> 语法：Promise.prototype.catch(onRejected)

```
    new Promise(function (resolve, reject) {
        if (/* 异步操作成功 */) {
            resolve(data);
        } else {
            /* 异步操作失败 */
            reject(error);
        }
    })
    .then(function(data){
        // 操作成功处理
    })
    .catch(function(error){
        // 操作失败处理
    })
```

> 作用：当 Promise 对象内部调用 `reject` 方法会触发 `.catch()` 执行代码

#### Promise 静态方法
##### .resolve()
> 语法: Promise.resolve()

```
Promise.resolve('Success');
/*******等同于*******/
new Promise(function (resolve) {
    resolve('Success');
});
```

##### .reject()
> 语法: Promise.reject()

```
Promise.reject(new Error('error'));

/*******等同于*******/
new Promise(function (resolve, reject) {
    reject(new Error('error'));
});
```

##### .all()
> 语法：Promise.all(iterable)

```
var p1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, "one"); 
}); 
var p2 = new Promise((resolve, reject) => { 
  setTimeout(reject, 2000, "two"); 
});
var p3 = new Promise((resolve, reject) => {
  reject("three");
});

Promise.all([p1, p2, p3]).then(function (value) {
    console.log('resolve', value);
}, function (error) {
    console.log('reject', error);    // => reject three
});
```
> 作用: 把多个 `Promise` 对象合并成一个 `Promise` 对象，多个 `Promise` 全部执行 `resolve` 才会触发合并后的 `Promise` 对象的 `.then()` 方法

##### .race()
> 语法：Promise.race(iterable)

```
var p1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, "one"); 
}); 
var p2 = new Promise((resolve, reject) => { 
  setTimeout(reject, 2000, "two"); 
});
var p3 = new Promise((resolve, reject) => {
  reject("three");
});

Promise.race([p1, p2, p3]).then(function (value) {
    console.log('resolve', value);
}, function (error) {
    console.log('reject', error);    // => reject three
});
```

> 作用: 把多个 `Promise` 对象合并成一个 `Promise` 对象，多个 `Promise` 只要有一个执行 `resolve` 就会触发合并后的 `Promise` 对象的 `.then()` 方法

#### 项目中常见用法
```
/* 
1. 定义任务操作并且包装成 Promise 对象
*/
// 任务1
function doLogin() {
    return new Promise((resolve,reject)=>{
        /*执行任务操作*/
        ajax('/login',function(data){
            if(/*判断登录成功*/) {
                resolve()
            } else {
                reject("登录失败原因")
            }
        })
    })
}

// 任务2
function doGetUserInfo() {
    return new Promise((resolve,reject)=>{
        /*执行任务操作*/
        ajax('getUserInfo',function(data){
            if(/*判断获取成功*/) {
                resolve()
            } else {
                reject("获取失败原因")
            }
        })
    })
}

// 任务3
function doGetUserPics() {
    return new Promise((resolve,reject)=>{
        /*执行任务操作*/
        ajax('getUserPics',function(data){
            if(/*判断获取成功*/) {
                resolve()
            } else {
                reject("获取失败原因")
            }
        })
    })
}

/*
2. 执行任务操作
*/
showLoading("操作中")
doLogin()                       // 登录
.then(doGetUserInfo)            // 获取用户信息
.then(doGetUserPics)            // 获取用户照片
.then(function(data){           
    hideLoading("操作成功")      // 处理最终结果
})
.catch(function(error){
    showError(error)           // 显示错误
})
```
