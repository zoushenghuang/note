### redux：一个专门用来做状态管理的JS库

作用: 集中式管理 react 应用中多个组件共享的状态。
可以构建一致化的应用，运行于不同的环境（客户端、服务器、原生应用），并且易于测试。
体小精悍（只有2kB，包括依赖），却有很强大的插件扩展生态

核心概念
Action
可以省略创建。
把数据从应用传到 store 的有效载荷，Action 是 store 数据的唯一来源。
一般来说会通过 store.dispatch() 将 action 传到 store。
有两个属性
type ：标识属性, 值为字符串, 唯一, 必要属性。
data ：数据属性, 值类型任意, 可选属性
Store
用来维持应用所有的 state 树 的一个对象，改变 store 内 state 的惟一途径是对它 dispatch 一个 action。
Store 不是类，它只是有几个方法的对象。 要创建它，只需要把根部的 reducing 函数传递给 createStore。
Reducers
指定了应用状态的变化如何响应 actions 并发送到 store 。
Reduce r函数会接收到两个参数，分别为：之前的状态、动作对象。
Reducer 有两个作用：初始化状态、加工状态。
Reducer 的第一次调用时，是store自动触发的，传递的 preState(之前的状态) 是undefined

### 高阶函数
满足这两个条件之一的就是高阶函数：
 接受一个或多个函数作为输入；
 输出一个函数；
比如JS中的map，filter，reduce

### Portals
是一种将子组件渲染到父组件 DOM 层次结构之外的技术。使用 Portals 可以在 React 应用中创建更灵活的 UI 布局，
用法: createPortal(child，container)：child是任何可渲染的React元素，container是DOM元素，也就是需要挂载的地方。

### Fragment组件
 - 因为要求只能有一个根节点，所以每次写结构都要包裹一个div。当我们不想多一个div结构时，可以采用Fragment
  Fragment标签可以用是用<></>代替(语法糖)。需要注意，当需要在Fragment标签绑定key属性时，不能采用语法糖的形式

### React Key
1.一个数组循环范围内，key属性的值，要保证唯一性
2.这个key属性，必须加载map() 的第一个子元素上，设置在深层次的元素上无效
3.不能使用索引值，虽然数组的索引是唯一的，但是在操作的时候，索引值可能会发生改变
### 【注意事项】
1.不要使用索引作为key值，因为索引是会动态变化的，所有列表元素都会重新渲染
2.key值要求唯一，且不会变化
3.不加key 值 浏览器会报错

### 受控组件与非受控组件
1. 受控组件是指其输入值由 React 组件的状态（state）进行控制的组件。在受控组件中，表单元素的值由组件的状态驱动，任何对输入值的更改都需要通过状态更新来实现。
2. 非受控组件是指其输入值不由 React 组件的状态控制，而是通过直接操作 DOM 元素来获取输入值。通常来说，非受控组件使用 React 的 ref 来访问 DOM 元素并获取其值

### Redux Toolkit 
1. npm install @reduxjs/toolkit
#### 使用方法
··1 定义store.js
  ```javascript
  import { configureStore } from '@reduxjs/toolkit'

  export const store = configureStore({
    reducer: {},
  })
  ```
..2 创建一个Redux State Slice(counterSlice文件)
```javascript
  import { createSlice } from '@reduxjs/toolkit'

  const initialState = {
    value: 0,
  }

  export const counterSlice = createSlice({
    name: 'counter',
    initialState,
    reducers: {
      increment: (state) => {
        // Redux Toolkit允许我们在reducers中直接写改变state的逻辑.
        // 由于使用了Immer库,所以并没有真的改变state
        // 而是检测到“草稿state”的更改并根据这些更改生成一个全新的不可变state
        state.value += 1
      },
      decrement: (state) => {
        state.value -= 1
      },
      incrementByAmount: (state, action) => {
        state.value += action.payload
      },
    },
  })

  // reducer方法的每一个case都会生成一个Action
  export const { increment, decrement, incrementByAmount } = counterSlice.actions

  export default counterSlice.reducer

```
..3 将Slice Reducers添加进Store
```javascript
  import { configureStore } from '@reduxjs/toolkit'
  import counterReducer from '../features/counter/counterSlice'

  export default configureStore({
    reducer: {
      counter: counterReducer,
    },
  })
```
..4 React中使用Redux Store 页面上调用
```javascript
  import { Provider } from "react-redux";
  <Provider store={store}>
    <App />
  </Provider>,

import { useSelector, useDispatch } from 'react-redux';
const count = useSelector((state) => state.counter.value); //获取store中的数据
const dispatch = useDispatch() //调用counterSlice中的方法
dispatch(increment()) //操作
```
# 总结
1、使用configureStore创建一个Redux Store

configureStore接收reducer方法作为命名参数，或者说作为reducer对象的属性
configureStore会使用默认设置自动设置好store

2、在React组件中使用 Redux store

用React-Redux提供的 <Provider>包裹你的<App />
使用 <Provider store={store}>传入Redux store

3、使用createSlice创建Redux “slice” reducer

createSlice需要有name属性作为唯一标识，还需要初始化state和已命名的reducer方法
Reducer方法通过使用Immer可以直接“改变”state，而不必手动做副本
导出生成的slice reducer 和 action creators

4、在React组件中使用React-Redux的useSelector/useDispatch钩子

使用useSelector钩子从store中读数据
使用useDispatch钩子获取到dispatch方法，并且根据需要dispatch actions
