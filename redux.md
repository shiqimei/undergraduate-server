# Redux学习笔记

## Motivation
Two important concepts: mutation and asynchronicity

````js
{
  todos: [{
    text: 'Eat food',
    completed: true
  }, {
    text: 'Exercise',
    completed: false
  }],
  visibilityFilter: 'SHOW_COMPLETED'
}
````
Redux 对于 JavaScript 应用而言是一个**可预测状态的容器**。换句话说，它是一个应用数据流框架。

Redux 用于应用状态的管理和数据流的处理。

## Core Concepts
Enforcing that every change is described as an action lets us have a clear understanding of what’s going on in the app.

?> reducer = state + actions

ruducer: a function that takes state and action as arguments, and returns the next state of the app.

## Three Principles
### Single source of truth
**The state of your whole application is stored in an object tree within a single store.**

原则一: 把所有的数据存在一个 store 对象中，它是一个单独的常量状态树。

````js
console.log(store.getState())
````

### State is read-only
**The only way to change the state is to emit an action, an object describing what happened.**

````js
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
})
````

### Changes are made with pure functions
**To specify how the state tree is transformed by actions, you write pure reducers.**

````js
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
const reducer = combineReducers({ visibilityFilter, todos })
const store = createStore(reducer)
````
]

## API

### combineReducers
组合一组 reducers，然后返回一个新的reducer。

### bindActionCreators
bindActionCreators的代码比较简单，就是将actionCreator和dispatch联结在一起。对于多个 actionCreator，我们可以像reducers一样，组织成一个key/action的组合。由于很多情况下，action是 actionCreator 返回的，实际上要这样调用 store.dispatch(actionCreator(...args))。

### compose
compose 调用了ES5的Array.prototype.reduce方法，将形如fn(arg1)(arg2)(arg3)...的柯里化函数按照顺序执行。

### createStore [Function]
创建一个 store。createStore接受3个参数：reducer, preloadedState, enhancer。reducer我们接下来会具体介绍，这里不过多赘述，第二个参数是preloadedState，它是state的初始值，实际上他并不仅仅是扮演着一个initialState的角色，如果我们第二个参数是函数类型，createStore会认为你忽略了preloadedState而传入了一个enhancer，如果我们传入了一个enhancer，createStore会返回enhancer(createStore)(reducer, preloadedState)的调用结果，这是常见高阶函数的调用方式。在这个调用中enhancer接受createStore作为参数，对createStore的能力进行增强，并返回增强后的createStore。

### action [Object]
这里插一个action，action代表的是用户的操作。redux规定action一定要包含一个type属性，且type属性也要唯一，相同的type，redux视为同一种操作，因为处理action的函数reducer只判断action中的type属性。

### reducer [Function]
Redux中负责响应action并修改数据的角色就是reducer，reducer的本质实际上是一个函数，其函数签名为：reducer(previousState，action) => newState。

### getState
获取当前的 state。

### subscribe
给store添加监听函数。nextListeners储存了整个监听函数列表。

### dispatch
在得到新的状态后，依次调用所有的监听器，通知状态变更。

### replaceReducer
1. 当你的程序要进行代码分割的时候
2. 当你要动态的加载不同的reducer的时候
3. 当你要实现一个实时reloading机制的时候

![](https://upload-images.jianshu.io/upload_images/6079494-89cba7656f3247dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

![](https://upload-images.jianshu.io/upload_images/6079494-d9d7088feffde333.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1000/format/webp)

Redux提供了applyMiddleware来装载middleware。

![](https://upload-images.jianshu.io/upload_images/6493119-f4c44eb340874014.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/454/format/webp)

## 参考链接
1. [理解Redux应用架构——（一）Redux结构概览](https://www.jianshu.com/p/e7937e1cfc05)

## Redux
Redux attempts to make state mutations predictable.

## Core Concepts
- The whole state of your app is stored in an object tree inside a single store.
- The only way to change the state tree is to emit an action, an object describing what happened.
- To specify how the actions transform the state tree, you write pure reducers.