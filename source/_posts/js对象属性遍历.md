---
title: js对象遍历
date: 2018-08-14 08:48:25
tags:
    - js
---
#  Object.keys

返回一个对象的所有**自身可枚举属性**的键值；

```js
const object1 = {
  a: 'somestring',
  b: 42,
  c: false
};

console.log(Object.keys(object1));
// expected output: Array ["a", "b", "c"]
```



# for...in

遍历对象的所有可枚举属性（**包含原型链上的属性**）；

```js
var obj = {a:1, b:2, c:3};
    
for (var prop in obj) {
  console.log("obj." + prop + " = " + obj[prop]);
}

// Output:
// "obj.a = 1"
// "obj.b = 2"
// "obj.c = 3"
```



# Object.entries

返回一个给定对象**自身可枚举属性**的键值对数组。

性质和Object.keys相同，只是结果不一样

```js
const object1 = { foo: 'bar', baz: 42 };
console.log(Object.entries(object1)[1]);
// expected output: Array ["baz", 42]

const object2 = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.entries(object2)[2]);
// expected output: Array ["2", "c"]

const object3 = { 100: 'a', 2: 'b', 7: 'c' };
console.log(Object.entries(object3)[0]);
// expected output: Array ["2", "b"]

```



#Object.getOwnPropertyNames()

返回一个数组，数组元素是对象**自身可枚举与不可枚举**属性的键值

```js
const object1 = {
  a: 1,
  b: 2,
  c: 3
};

console.log(Object.getOwnPropertyNames(object1));
// expected output: Array ["a", "b", "c"]

```







```js
class ObjCreater{
    constructor(props) {
        const {a,b} = props;
        this.a = a;
        this.b = b;
    }
}

ObjCreater.prototype.d = '静香';

const obj = new ObjCreater({a:'大雄',b:'胖虎'});

Object.defineProperty(obj,'c',{
    value:'哆啦A梦',
    enumerable: false,
});

console.log(Object.getOwnPropertyNames(obj)); //  ['a','b','c']  c是不可枚举属性
console.log(Object.keys(obj)); // ['a','b']  a,b是可枚举属性

const keys = []
for(let prop in obj){
    keys.push(prop)
}
console.log(keys)
// ['a','b'，‘d’]   d是原型链上的属性

console.log(Object.entries(obj)); //[ [ 'a', '大熊' ], [ 'b', '胖虎' ] ]
```





# Refrence

[for...in  MDN](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in)

