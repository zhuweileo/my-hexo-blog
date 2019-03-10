---
title: 原生实现promise
date: 2019-03-07 21:43:18
tags: 
    - js
    - 前端
---
```js
/* ----------------------------------------------------------------------------
 * Promise 完整版
 --------------------------------------------------------------------------- */
const states = {
   PENDING  : 'pending',
   FULFILLED: 'fulfilled',
   REJECTED : 'rejected'
}

class Promise {
   /**
    * [ 构造函数 ]
    * @param  {[Function]} excurtor [执行函数]
    * @return {[Undefined]} undefined
    */
   constructor (excurtor) {
       let self = this;

       this.status      = states.PENDING; // 当前状态
       this.value       = undefined;      // 成功的内容
       this.reason      = undefined;      // 失败的原因
       this.onFulfilled = [];             // 存储成功回调
       this.onRejected  = [];             // 存储失败回调

       // 成功方法
       function resolve (value) {
           let onFulfilled = self.onFulfilled;

           // 判断状态是否为pending，只有此状态时，才可以发生状态改变
           if (self.status === states.PENDING) {
               self.status = states.FULFILLED;
               self.value = value;

               if (onFulfilled.length) {
                   onFulfilled.forEach(fn => {
                       fn();
                   });
               }
           }
       }

       // 失败方法
       function reject (reason) {
           let onRejected = self.onRejected;

           // 判断状态是否为pending，只有此状态时，才可以发生状态改变
           if (self.status === states.PENDING) {
               self.status = states.REJECTED;
               self.reason = reason;

               if (onRejected.length) {
                   onRejected.forEach(fn => {
                       fn()
                   });
               }
           }
       }

       // 立即调用执行函数，并捕获异常
       try {
           excurtor(resolve, reject);
       } catch (e) {
           reject(e); // 捕获到执行函数异常后，直接执行失败回调
       }
   }

   /**
    * [ 实例方法: then ]
    * @param  {[Function]} onFulfilled [成功回调]
    * @param  {[Function]} onRejected  [失败及异常回调]
    * @return {[Object]}  promise2
    */
   then (onFulfilled, onRejected) {
       // 补全参数，实现往下传递
       onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
       onRejected = typeof onRejected === 'function' ? onRejected : error =>{ throw error };

       let self = this;
       let promise2;

       promise2 = new Promise((resolve, reject) => {
           // 状态已改变为成功时，则立即执行
           if (self.status === states.FULFILLED) {
               setTimeout(() => { // 此定时器确保回调方法在异步中执行的，也方便进行promiseA+规范测试
                   // try catch 用来捕获回调方法中抛出的异常
                   try {
                       let x = onFulfilled(self.value);
                       resolvePromise(promise2, x, resolve, reject);
                   } catch (e) {
                       reject(e);
                   }
               });
           }

           // 状态已改变为失败时，则立即执行
           if (self.status === states.REJECTED) {
               setTimeout(() => { // 同上
                    // 同上
                   try {
                       let x = onRejected(self.reason);
                       resolvePromise(promise2, x, resolve, reject);
                   } catch (e) {
                       reject(e);
                   }
               }, 0);
           }

           // 处理异步情况，先存在起来，等状态改变再执行相应回调
           if (self.status === states.PENDING) {
               self.onFulfilled.push(() => {
                   setTimeout(() => {  // 同上
                        // 同上
                       try {
                           let x = onFulfilled(self.value);
                           resolvePromise(promise2, x, resolve, reject);
                       } catch (e) {
                           reject(e);
                       }
                   }, 0);
               });
               self.onRejected.push(() => {
                   setTimeout(() => {  // 同上
                        // 同上
                       try {
                           let x = onRejected(self.reason);
                           resolvePromise(promise2, x, resolve, reject);
                       } catch (e) {
                           reject(e);
                       }
                   }, 0);
               });
           }
       });

       return promise2;
   }

   /**
    * [ 实例方法：catch ]
    * @param  {[Function]} onRejected [失败及异常回调]
    * @return {[Object]}   promise
    */
   catch (onRejected) {
       return this.then(null, onRejected);
   }

   /**
    * [ 静态方法(由类调用)：all ]
    * @param  {[Array]} promises [由promise组成的数组]
    * @return {[Object]} promise
    */
   static all (promises) {
       let res = [];
       let count = 0;
       let length = promises.length;

       return new Promise((resolve, reject) => {
           promises.forEach((promise, index) => {
               promise.then(
                   value => {
                       res[index] = value;
                       if (++count === length) {
                           resolve(res);
                       }
                   },
                   reject
               );
           });
       });
   }

   /**
    * [ 静态方法(由类调用)race ]
    * @param  {[Array]} promises [由promise组成的数组]
    * @return {[Object]} promise
    */
   static race (promises) {
       return new Promise((resolve, reject) => {
          promises.forEach(promise => {
              promise.then(resolve, reject);
          })
       });
   }
}

function resolvePromise(promise2, x, resolve, reject) {
    // 自己不能等待自己的状态改变
    if (promise2 === x) {
        return reject(new TypeError('循环引用'));
    }

    let called; // 标识位，要么成功，要么失败；
    let typeX = typeof x;

    // 判断x的类型是否为对象或者函数且不为null
    if (x !== null && (typeX === 'object' || typeX === 'function')) {
        try {
            let then = x.then; // 获取then方法； 此处的try用于防止第三方库中获取值会抛错

            if (typeof then === 'function') { // 说明返回的是promise事例
                // 执行then方法，并将this指向x
                then.call(x, y => {
                    if (called) return;
                    called = true;
                    // 如果返回的还是promise, 则将会继续递归调用
                    resolvePromise(promise2, y, resolve, reject);
                }, e => {
                    if (called) return;
                    called = true;
                    reject(e);
                });
            } else { // x为变通对象时，直接成功
                resolve(x);
            }
        } catch (e) {
            if (called) return;
            called = true;
            reject(e);
        }
    } else {
        resolve(x);
    }
}

// 为了方便promiseA+规范测试
Promise.defer = Promise.deferred = function () {
    let dtd = {};
    dtd.promise = new Promise((resolve, reject) => {
        dtd.resolve = resolve;
        dtd.reject = reject;
    });
    return dtd;
};


module.exports = Promise;



// 测试例子 ---------------------------------------------------------------------
let promise = new Promise((resolve, reject) => {
   resolve(1000);
   //reject(100);
});

promise
.then(
   value => {
       console.log('then:', value);
       return new Promise((resolve, reject) => {
           //resolve(199);
           reject('error123');
       });
   },
   reason => console.log(`fail: ${ reason }`)
)
.then(
   value => console.log(value),
   reason => console.log(`fail: ${ reason }`)
);

Promise.all([
    new Promise((resolve, reject) => {
        resolve(1);
    }),
    new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(2);
        }, 1000);
    }),
    new Promise((resolve, reject) => {
        resolve(3);
        //reject('出错了')
    })
]).then(
    value => console.log(value),
    e => console.log(e)
);


```
