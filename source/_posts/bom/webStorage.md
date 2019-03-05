---
title: Web Storage API
date: 2019-03-05 21:50:32
tags:
    - 前端
    - 浏览器
---

# 作用
Web storage API使页面可以将一些数据保存在`客户端`。

# 种类
- sessionStorage
- localStorage

# sessionStorage和localStorage的区别

## sessionStorage
当前页面刷新、恢复（通过浏览器的‘恢复上次关闭的页面’,不是手动重新打开）时，被保存的数据仍然保留;
关闭浏览器窗口时，数据清空。

## localStorage
刷新、恢复、关闭浏览器窗口，数据都会保留。

# 方法
sessionStorage,localStorage都包含以下4种方法
## setItem
```js
//存一个值
localStorage.setItem('name','leo');
```
## getItem
```js
//读取一个值
localStorage.getItem('name');
```
### removeItem
```js
//删除一个值
localStorage.removeItem('name');
```
### clear
```js
//清空
localStorage.clear();
```
> ps：storage中的键值对，key和value都必须是string类型，如果不是，会被自动转为string


# storage事件

> Storage 对象发生变化时（即创建/更新/删除数据项时，重复设置相同的键值不会触发该事件，Storage.clear() 方法至多触发一次该事件），StorageEvent 事件会触发。在同一个页面内发生的改变不会起作用——在相同域名下的其他页面（如一个新标签或 iframe）发生的改变才会起作用。在其他域名下的页面不能访问相同的 Storage 对象。

**例如:**
http://111.11.11:8080/a.html
```js
localStorage.setItem('name','leo')
```
http://111.11.11:8080/b.html
```js
window.addEventListener('storage',function(e){
}) 
```
同时打开 同源的 a,b两个页面，a页面内设置localStorage,b页面内会出发storage事件。

> 但是我通过测试，发现sessionStorage并不会触发这个事件




