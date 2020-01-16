---
title: React小白学习
tags: [React]
categories: Web
---

## 基本概念

- **React.js**

  React.js 是 React 的核心库，在应用中必须先加载核心库。

- **ReactDOM.js**

  ReactDOM.js 是 React 的 DOM 渲染器，React 将核心库和渲染器分离开了，为了在 web 页面中显示开发的组件，需要调用 ReactDOM.render 方法， 第一个参数是 React 组件，第二个参数为 HTMLElement

- **JSX**

  <a herf ="<https://segmentfault.com/a/1190000005145610>">JSX 是 React 自定义的语法，最终 JSX 会转化为 JS 运行于页面当中。</a>

- **组件**

  <a href="https://segmentfault.com/a/1190000005161417">组件是 React 中的核心概念，页面当中的所有元素都是通过 React 组件来表达， 我们将要写的 React 代码绝大部分都是在做 React 组件的开发 </a>

- **VIRTUAL DOM**

  React 抽象出来的虚拟 DOM 树 （其实就是内存中的一个**js 对象**），虚拟树是 React 高性能的关键。

- **单向数据流：one-way reactive data flow**

  React 应用的核心设计模式，数据流向自顶向下

  ### react 工作原理

  1. state,props 数据
  2. jsx 模版
  3. 数据+ 模版 生成虚拟 DOM(就是一个 js 对象，用来描述真实 DOM) eg: { 'div', { id : 'abc' }, ['span',{},'hello']}
  4. 用虚拟 DOM 结构生成真实 DOM,来显示 eg : <div id='abc'><span>hello</span></div>
  5. state 发生变化
  6. 数据+模版 生成 **新的** 虚拟 DOM eg : { 'div', { id : 'abc' }, ['span',{},'bye']}
  7. 比较原始虚拟 DOM 和新虚拟 DOM 的区别 (用到 diff 算法，同层比对)，找到区别 eg: span 中内容
  8. 直接操作 DOM, eg : 改变 span 中内容

## 组件生命周期

​ [https://raw.githubusercontent.com/pszh/webLearn/master/%E6%9C%89%E9%81%93%E5%9B%BE%E7%89%87/react%E7%BB%84%E4%BB%B6%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F.png](https://raw.githubusercontent.com/pszh/webLearn/master/有道图片/react组件生命周期.png)

### componentWillMount

- **条件**：第一次渲染阶段在调用 render 方法前会被调用
- **作用**：该方法在整个组件生命周期只会被调用一次，所以可以利用该方法做一些组件内部的初始化工作

### componentDidMount

- **条件**：第一次渲染成功过后，组件对应的 DOM 已经添加到页面后调用
- **作用**：此时组件对应的 DOM 已经存在，我们可以在这个时候做一些依赖 DOM 的操作或者其他的一些如请求数据，和第三方库整合的操作。如果嵌套了子组件，子组件会比父组件优先渲染，所以这个时候可以获取子组件对应的 DOM

### componentWillReceiveProps(newProps)

- **条件**：当组件获取新属性的时候，第一次渲染不会调用
- **作用**：这个时候可以根据新的属性来修改组件状态

```jsx
componentWillReceiveProps: function(nextProps) {
  this.setState({
  	isShow: nextProps.likeCount > this.props.likeCount
  });
}
```

- **注意**：这个时候虽说是获取新属性，但并不能确定属性一定改变了，例如一个组件被多次渲染到 DOM 中

```jsx
eg:var Component = React.createClass({
        componentWillReceiveProps: function(nextProps) {
            console.log('componentWillReceiveProps', nextProps.data.bar);
        },
        rener: function() {
            return <div> {this.props.data.bar} </div>
        }
    });

    var container = document.getElementById('container');
    var mydata = {bar: 'drinks'};
    ReactDOM.render(<Component data={mydata} />, container);
    ReactDOM.render(<Component data={mydata} />, container);
    ReactDOM.render(<Component data={mydata} />, container);
    结果会输出两次 componentWillReceiveProps，虽然属性数据没有改变，但是仍然会调用 componentWillReceiveProps 方法。
```

### shouldComponentUpdate(nextProps, nextState)

- **条件**：接收到新属性或者新状态的时候在 render 前会被调用（除了调用 forceUpdate 和初始化渲染以外）
- **作用**：该方法让我们有机会决定是否重渲染组件，如果返回 false，那么不会重渲染组件，借此可以优化应用性能

### componentWillUpdate(nextProps, nextState)

- **条件**：当组件确定要更新，在 render 之前调用
- **作用**：这个时候可以确定一定会更新组件，可以执行更新前的操作
- **注意**：方法中**_不能使用 setState_** ，setState 的操作应该在 componentWillReceiveProps 方法中调用

### componentDidUpdate(prevProps, prevState)

- **条件**：更新被应用到 DOM 之后
- **作用**： 可以执行组件更新过后的操作

​

## React 与 DOM

### 获取 DOM 元素

从组件的生命周期中，Dom 真正添加到 html 中 hook 是，

- _componentDidMount_
- _componentDidUpdate_

这两个函数中可以获取到，react 提供了两种获取方式

1. **findDOMNode()**

   ```jsx
   var MyComponent = React.createClass({
     render: function() {
       return <div> .... </div>;
     },
     componentDidMount: function() {
       var $root = ReactDOM.findDOMNode(this);
       console.log($root);
     }
   });
   ```

   **注意**：此方法不能用到无状态组件上，且只能获取到 root 元素，（如果 dom 多层级，获取子级元素需要 refs）

2. **Refs**

   每个组件都有 this.refs 属性，

   ```jsx
   var MyComponent = React.createClass({
     render: function() {
       return (
         <div>
           <button ref="btn">...</button>
           <a href="" ref="link"></a>
         </div>
       );
     },
     componentDidMount: function() {
       var $btn = this.refs.btn;
       var $link = this.refs.link;
       console.log($btn, $link);
     }
   });
   ```

## redux

**redux 的引入**：不同组件之间的通讯时 由于 **层级太深** 导致麻烦， 所以引入 redux 的 Store 统一管理

redux = flux + reducers

### redux 的工作流

[https://github.com/pszh/webLearn/blob/master/%E6%9C%89%E9%81%93%E5%9B%BE%E7%89%87/redux%20%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B.png](https://github.com/pszh/webLearn/blob/master/有道图片/redux 工作流程.png)

**描述** ：

1. 组件通过 dispatch 发起 action

   2. store 把之前的数据 state 和 actions 转发给 reducers
   3. reducers 根据 actions.type 操作 state，并返回新 state 到 store 中
   4. store 把 新 state 替换老 state
   5. store 发生改变 组件 会感知到改变，然后 setState _( 组件要注册 store.subscribe()监听)_

### 代码中使用

#### 安装

```
	npm install redux —save
```

### 编写

1.  src 下建 store 文件夹 =》 index.js state 中心

    ```js
    import { createStore } from "redux";
    //第二步创建的
    import reducer from "./reducer.js";
    const store = createStore(reducer);
    export default store;
    ```

2.  store => reducer.js state 操作，管理中心

    ```js
    //默认初始化的state 状态
    const defaultState = {
      inputValue: ""
    };
    export default (state = defaultState, action) => {
      switch (action.type) {
        case "changeInputValue":
          return {
            ...state,
            inputValue: action.value
          };
          break;
      }
      return state;
    };
    ```

3.  组件中引入

    ```js
    import  store form './store';

    // 组件的 constructor 构造方法中
    constructor(props){
      super(props);
      this.handleInputChange = this.handleInputChange.bind(this);
      this.handleStoreChange = this.handleStoreChange.bind(this);

    	this.state = store.getState();
      //订阅store的改变 dispacth之后store改变
      store.subscribe(()=>{
        this.setState(store.getState());
      });
    }

    //发起action
    handleInputChange(value){
      const actions = {
        type : 'changeInputValue',
        value: value
      }
      store.dispatch(actions);
    }
    ```

    ### **opt** :

    1. 这个里面的 actions.type 可以统一管理， 在 src/store 建一个 actionsTypes.js 文件，然后去引用对应变量

       ```js
       export const CHANGE_INPUT_VALUE = "changeInputValue";
       ```

    2. actions 在 组建中去创建太过分散，统一管理 ，在 src/store 建一个 actionsCreator.js 文件，

       ```js
       import { CHANGE_INPUT_VALUE } from "./actionTypes";
       //对应组件中使用这个去创建对应action
       export const getInputChangeAction = value => ({
         type: CHANGE_INPUT_VALUE,
         value
       });
       ```


       //组件中的 handleInputChange
       handleInputChange(value){
         const actions = getInputChangeAction(value)
         store.dispatch(actions);
       }
       ```

**注意** ：

- store 是唯一的 （整个项目就一个 createStore()）
- 只有 store 才能改变 (reducer 中返回的 state 也是给到 store 中改变 state 的)
- reducer 函数必须是纯函数，（固定输入，就有固定输出，不能有异步操作等，）

复习：四个函数 createStore() ; getState() ; dispatch() ; subscribe()

### redux 异步操作 redux—thunk

- **原理**：可以让 actions 是一个函数，

- **安装**：npm install redux-thunk —save

- **引入**：src/store/index.js

```js
import { createStore,applyMiddleware, compose } from 'redux';
import thunk from 'redux-thunk';
//第二步创建的
import reducer from './reducer.js';

// 下面两个为了使用redux-devtools这个浏览器插件
const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__?window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
const enhancer = composeEnhancers(
	applyMiddleware(thunk),
);
const  store = createStore( reducer,enhancer );
//不使用浏览器插件的话
//const  store = createStore( reducer, applyMiddleware(thunk) );
export default store;
```

- **使用**：src/store/actionCreators.js

```js
import { CHANGE_INPUT_VALUE, GET_HTTP_DATA } from "./actionTypes";
import axios from "axios";
//普通的 action
export const getInputChangeAction = value => ({
  type: CHANGE_INPUT_VALUE,
  value
});

//异步action
export const getHttpData = () => {
  return (dispatch, getState) => {
    axios.get("/list.json").then(res => {
      const data = res.data;
      //调用一个普通action 去reducer
      const action = getInputChangeAction(data);
      dispatch(action);
    });
  };
};

//组件里面需要掉用的是
store.disPatch(getHttpData());
```

### redux 异步操作 redux—saga

- **原理**：通过捕获，拦截对应的 action，然后调用自己的（异步）方法， 这个方法中再去发起另一个 aciotns 出发 reducer
- **安装**：npm install redux—saga —save
- **引入**：src/store/index.js

  ```js
  import { createStore, applyMiddleware, compose } from "redux";
  import createSagaMiddleware from "redux-saga";
  import reducer from "./reducer.js";
  //异步操作的集合
  import Sagas from "./sagas";
  // create the saga middleware
  const sagaMiddleware = createSagaMiddleware();
  // mount it on the store
  const store = createStore(reducer, applyMiddleware(sagaMiddleware));
  // run the saga
  sagaMiddleware.run(Sagas);
  export default store;
  ```

src/store/sagas.js

```js
import { takeEvery, put } from 'redux-saga/effects';
import { CHANGE_INPUT_VALUE, GET_HTTP_DATA } from './actionTypes';
import { getInputChangeAction } form './actionCreators';
import axios from 'axios';


function* getHttpData(){
  const res = yield axios.get('/list.json');
  // 发起一个新actions 出发reducer
  const actions = getInputChangeAction(res.data);
  // 不是用dispatch 了
  yield put( actions );
}

// generator 函数
function * Sagas(){
  // 拦截到 action.type = GET_HTTP_DATA, 调用getHttpData函数
  yield takeEvery(GET_HTTP_DATA,getHttpData)
}
export default Sagas;
```

- **使用**：组件中

  ```js
  store.dispatch({ type: GET_HTTP_DATA });
  ```

### React-redux

- **简介** ：<a href="https://react-redux.js.org/introduction/quick-start">第三方模块，更方便的在 react 组件中使用 redux</a>

- **安装**：

  ```js
  npm install react-redux
  ```

- **使用**：

  1. provider

     ```js
     import React from "react";
     import ReactDOM from "react-dom";
     import TodoList from "./TodoList";
     import { Provider } from "react-redux";
     import store from "./store";

     const App = (
       <Provider store={store}>
         <TodoList />
       </Provider>
     );

     const rootElement = document.getElementById("root");
     ReactDOM.render(App, rootElement);
     ```

  2. connect()

     ```js
     import React, { Component } from "react";
     import { connect } from "react-redux";
     import { getInputChangeAction } from "./actionCreators";

     class TodoList extends Component {
       constructor(props) {
         super(props);
       }
       render() {
         return (
           <div>
             <div>
               <input
                 value={this.props.inputValue}
                 onChange={this.props.handleInputChange}
               />
             </div>
           </div>
         );
       }
     }
     /**把store中的state转到组件的props**/
     const mapStateToProps = state => {
       return {
         inputValue: state.inputValue
       };
     };
     /** store.dispatch, props**/
     const mapDispatchToProps = dispatch => {
       return {
         handleInputChange(e) {
           const action = getInputChangeAction(e.target.value);
           dispatch(action);
         }
       };
     };
     export default connect(mapStateToProps, mapDispatchToProps)(TodoList);
     ```

     **注意**：Provider 提供了 store,里面的组件才可以使用 connect()

### OPT

​ 项目变大的时候所有的代码逻辑都放入到单个 reducer 函数中都将会让程序变得不可维护，所以需要拆分开

1.  查分到每一个组件的上面，每个组件上建立一个 store/reducer.js 文件，合并用到**combineReducers**

    ```js
    //合并 reducer 代码

    import { combineReducers } from 'redux';
    // 拆分出来的reducer ,里面的写法和 之前的src/store/reducer.js 一样
    import headerReducer form '../common/header/store/reducer';

    export default combineReducers({
      header:  headerReducer
    });

    // 这个时候组件中的mapstateToprops 要用 state.header.*** 去赋值了
    ```

2.  数据的 state 有可能不小心被改变了，这个时候可以引入一个<a href="https://immutable-js.github.io/immutable-js/">**immutable 组件**</a>，然后用 redux-immutable 去统一数据格式

    ```js
    //安装
    npm install immutable --save

    // 然后是 reducer.js 文件
    import { fromJs } from 'immutable';
    const defaultState = fromJs({
      inputValue:'',
    })
    export default (state = defaultState ,action ) => {
      switch(action.type){
        case 'changeInputValue':
          	//设置值
          	return state.set('inputValue',action.value);
          break;
      }
      return state;
    }

    // 组件中
    mapstateToprops=(state)=>{
      return {
      	//没有拆分 reducer 时
        inputValue: state.get('inputValue'),
    		// 拆分reducer时 （前提引入react-immutable）
        focuse: state.get('header').get('focuse'),
        //或
        focuse: state.getInt(['header' , 'focuse']),
      }
    }

    // 安装 react-immutable
    npm install react-immutable

    // 组件的reducer 中 (下面的引入改变)
    import { combineReducers } from 'redux-immutable';
    // 拆分出来的reducer ,里面的写法和 之前的src/store/reducer.js 一样
    import headerReducer form '../common/header/store/reducer';

    export default combineReducers({
      header:  headerReducer
    });
    ```

## react-router

    ### 安装：

```js
npm run react-router-dom
// react-router-dom 基于react-router，加入了在浏览器运行环境下的一些功能，例如：Link组件，会渲染一个a标签，Link组件源码a标签行; BrowserRouter和HashRouter组件，前者使用pushState和popState事件构建路由，后者使用window.location.hash和hashchange事件构建路由
```

### 使用：

```js
import React, { Component } from "react";
import { Provider } from "react-redux";
import { BrowserRouter, Route } from "react-router-dom";
import store from "./store";
class App extends Component {
  render() {
    return (
      <Provider store={store}>
        <BrowserRouter>
          <div>
            <Route path="/" exact render={() => <div> home </div>} />
            <Route path="/detail" exact render={() => <div> detail </div>} />
          </div>
        </BrowserRouter>
      </Provider>
    );
  }
}
```

### api 文档 ：

<https://blog.csdn.net/debbyDeng/article/details/84555817>

- **Route**: 最重要的模块，主要职责是当 location 匹配路由时，会将 UI render 出来

- **Link**: 进入页面路由的链接

- **Router**: 所有路由组件最底层的接口，

- **redirect**:跳一个新路由，新 location 将覆盖 history stack 中的当前 location

- **NavLink**: link 的特殊版本，当匹配当前 url 时，会给当前 Link 添加样式

- **prompt**: 阻止用户跳转路由时的提示，配合 router 的 getUserConfirmation 使用

-

* **MemoryTouter**：把 url 保存在内存中，RN，测试等使用

##

​
