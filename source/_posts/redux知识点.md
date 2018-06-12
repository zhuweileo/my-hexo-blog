---
title: redux知识点
date: 2018-06-05 20:15:01
tags: 
 - redux 
 - react
---
## redux概念复习

1. Action

   actions 只是描述了*有事情发生了*这一事实，并没有描述应用如何更新 state 

   1.1

   store 数据的**唯一**来源 ；

   一般来说你会通过 [`store.dispatch()`](https://cn.redux.js.org/docs/api/Store.html#dispatch) 将 action 传到 store ；

   1.2

   Action 创建函数，Action 创建函数 就是生成 action 的方法。

   ```js
   function addTodo(text) {
     return {
       type: ADD_TODO,
       text
     }
   }
   ```

2. Reducer

   2.1 数据state与ui state 相分离

   2.2 reducer禁忌：

   - 修改传入参数；
   - 执行有副作用的操作，如 API 请求和路由跳转；
   - 调用非纯函数，如 `Date.now()` 或 `Math.random()`。

   2.3  [`combineReducers()`](https://cn.redux.js.org/docs/api/combineReducers.html) 将多个 reducer 合并成为一个 。

   ```js
   import { combineReducers } from 'redux'
   
   const todoApp = combineReducers({
     visibilityFilter,
     todos
   })
   
   export default todoApp
   ```
   等价于
   ```js
   export default function todoApp(state = {}, action) {
     return {
       visibilityFilter: visibilityFilter(state.visibilityFilter, action),
       todos: todos(state.todos, action)
     }
   }
   ```

   ```js
   const reducer = combineReducers({
     a: doSomethingWithA,
     b: processB,
     c: c
   })
   ```
   等价于
   ```js
   function reducer(state = {}, action) {
     return {
       a: doSomethingWithA(state.a, action),
       b: processB(state.b, action),
       c: c(state.c, action)
     }
   }
   ```

   

3. Store

   3.1  Redux 应用只有一个单一的 store

   3.2 生成store

   1. 通过combineReducers() 将多个 reducer 合并成为一个根 reducer
   2. 调用createStore( reducer ) 方法，生成store

   ```js
   import { createStore } from 'redux'
   import todoApp from './reducers'
   let store = createStore(todoApp)
   ```

   3.3 store的功能

   - 维持应用的 state；
   - 提供 [`getState()`](https://cn.redux.js.org/docs/api/Store.html#getState) 方法获取 state；
   - 提供 [`dispatch(action)`](https://cn.redux.js.org/docs/api/Store.html#dispatch) 方法更新 state；
   - 通过 [`subscribe(listener)`](https://cn.redux.js.org/docs/api/Store.html#subscribe) 注册监听器;
   - 通过 [`subscribe(listener)`](https://cn.redux.js.org/docs/api/Store.html#subscribe) 返回的函数注销监听器。

   ```js
   // 每次 state 更新时，打印日志
   // 注意 subscribe() 返回一个函数用来注销监听器
   const unsubscribe = store.subscribe(() =>
     console.log(store.getState())
   )
   
   // 发起一系列 action
   store.dispatch(addTodo('Learn about actions'))
   store.dispatch(addTodo('Learn about reducers'))
   store.dispatch(addTodo('Learn about store'))
   store.dispatch(toggleTodo(0))
   store.dispatch(toggleTodo(1))
   store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))
   
   // 停止监听 state 更新
   unsubscribe();
   ```

