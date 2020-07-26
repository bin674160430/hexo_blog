---
title: React 
date: 2020-07-19 20:48:00
categories:
    - 框架
tags:
    - React
---

# [React 虚拟 DOM实现原理](https://blog.csdn.net/huangpb123/article/details/84947475?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522159516293119195239819666%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=159516293119195239819666&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~first_rank_ecpm_v3~pc_rank_v2-2-84947475.first_rank_ecpm_v3_pc_rank_v2&utm_term=react%E7%9A%84%E8%99%9A%E6%8B%9Fdom%E5%8E%9F%E7%90%86)

- 虚拟`DOM`本质是`JavaScript`对象，是对`DOM`的封装（`DOM`操作耗时，`js`高效）
- 状态变更时，通过`diff`算法记录新树和旧树的差异
- 将差异更新到`DOM`中

# [Redux的实现](https://www.cnblogs.com/pluslius/p/10580727.html)

组件发出`action`，状态管理器接收到`action`，使用`reducer`函数算出新的状态

1. `reducer`函数 核心`api`，根据现有state和action返回一个新的`state`，交给`store`

2. `action`

   ```javascript
   // 对象，同步
   export const action1 = {
       type: 'action1',
       data: {}
   }
   // 函数，异步，返回的函数接收一个dispatch参数
   export const login = () => {
       return dispatch => {
           
       }
   }
   ```

3. `store` 管理整个`state`, 主要方法 `getState(), dispatch(action-creator), subsribe(listener)`

在项目开发中，使用react-redux来简化react中使用redux

```jsx
// 向所有组件提供store
<Provider store={store}>...</Provider>

class MyComponent extends Component {
    
}
connect(
	state => { ... }, // 从state中获取属性
    dispatch => { ... } // 执行dispatch分发action
)(MyComponent)
```



{% image redux.png 示例图 %}

```javascript
// 创建store, 一个数据容器
const store = createStore(reducerFn);

// state 数据集合
const state = store.getState();

// action, 一个对象, type是必须的，表示Action名称，其它属性可以自由设置
const action = {
    type: 'Add_TODO',
    payload: 'Learn Redux'
}
// view要发送多小种消息，就会有多少种action，可以定义一个函数来生成action，这个函数叫Action Greator
const ADD_TODO = 'add to do';
function addTodo(text) {
    return {
        type: ADD_TODO,
        text
    }
}

const action = addTodo('Learn Redux');

// 发起action
store.dispatch(addTodo('Learn Redux'));

// Reducer, store收到action以后，由reducer函数处理，给出一个新的state
const reducer = function(state, action) {
    // ...
    return new_state;
}

// 工作流程
// 先发起action
store.dispatch(action);

// store自动调用reducer，并且传入两个参数：当前state和收到的action，reducer会返回新的state
let nextState = todoApp(preState, action)

// state 一旦有变化，store就会调用监听函数
store.subscribe(listener)

function listener() {
    let newState = store.getState();
    component.setState(newState);
}
```

`redux`中间件就是对`store`的`dispatch`升级处理支持对象和函数，常用的中间件有 `redux-thunk`, `redux-saga` 用来解决`redux`中异步操作，`redux-thunk`是把异步操作放在action里操作，`redux-saga`是把异步操作单独拆分出来放在一个文件操作

## createStore

```javascript
function createStore(reducer, initState, enhancer) {
    // 接收state，添加观察者
    let state = initState;
    let listeners = [];
    
    // 获取state
    function getState() {
        return JSON.parse(JSON.stringify(state))
    }
    
    // 造一个dispatch来发送action
    function dispatch(action) {
        state = reducer(state, action);
        listeners.forEach(listener => listener());
    }
    
    // 添加订阅者
    function subscribe(listener) {
        listeners.push(listener);
        return function() {
            listeners.filter(item => item != listener)
        }
    }
    
    return {
        getState,
        dispatch,
        subscribe
    }
}
```

## combineReducer

```javascript
// 先拿到reducer
function combineReducer(reducer) {
    // 把state和action交给reducer处理
    return (state={}, action) => (
        // 拿到对应reducer的处理结果把原来的属性拷贝到新对象上
    	Object.keys(reducer).reduce((cur, key) => {
            cur[key] = reducer[key](state[key], action);
            return cur;
        }, {})
    )
}
```

## bindActionsCreators

```javascript
function bindActionsCreators(actions, dispatch) {
    // 先拿到actions和dispatch
    return Object.keys(actions).reduce((obj, key) => {
        obj[key] = function() {
            // 等执行的时候再调用真正的actions和dispatch
            dispatch(actions[key].apply(null, arguments))
        }
    }, {})
}
```

## ApplyMiddleware

在`action`之后，`reducer`处理之前

```javascript
// compose 先执行栈顶的middleware，然后回流到栈底的middleware
const compose = middlewares => middlewares.reduce((a, b) => (
	(...args) => a(b(...args))
))

// 接收中间件
function applyMiddleware(...middlewares) {
    // 接收store方法
    return function(createStore) {
        // 接收reducer
        return function(reducer) {
            let store = createStore(reducer);
            let dispatch;
            let middlewareAPI = {
                getState: store.getState,
                dispatch: action => dispatch(action)
            }
            // 把state 和 dispatch 方法传给中间件的一个函数
            middlewares = middlewares.map(middleware => middleware(middlewareAPI));
            dispatch = compose(...middlewares)(store.dispatch);
            return {
                ...store,
                dispatch // 先执行middleware1
            }
        }
    }
}

const middleware1 (dispatch) => {
    (action) => {
        console.log('middleware1');
        dispatch(action);
        console.log('after middleware1');
        return res;
    }
}

const middleware2 (dispatch) => {
    (action) => {
        console.log('middleware1');
        dispatch(action);
        console.log('after middleware2');
        return res;
    }
}

// middleware1 -> middleware2 -> action -> middleware2 -> middleware1
let middlewares = [middleware1,middleware2];

const dispatch = (action) => {
    console.log(`action: ${action}`);
    return action;
}

// 这是最后一个中间件，调用它将返回第一个中间件
const afterDispatch = compose(middlewares)(dispatch);

const testAction = args => ({
    type: "ACTION_TEST",
    args
})

afterDispatch(testAction('plus!'))
```

## react-redux

```javascript
class Provider extends React.Component {
    // 静态属性childContextTypes声明提供给子组件的Context对象属性，
    // 并实现一个实例getChildContext，返回一个Context纯对象
    static childContextTypes = {
        store: propTypes.object.isRequired
    }
	getChildContext() {
        return {
            store: this.props.store
        }
    }
	render() {
        return this.props.children;
    }
}

function Connect(mapStateToProps, mapDispatchToProps) {
    return function(WrapedComponent) {
        class ProxyComponent extends React.Component {
            static contextType {
                store: propTypes.object
            }
        	constructor(props, context) {
                super(props, context);
                this.store = context.store;
                this.state = mapStateToProps(this.store.getState);
            }
        	componentWillMount() {
                // 当state变化的时候通过setState更新组件的变化
                this.unsubscribe = this.store.subscribe(() => {
                    this.setState(mapStateToProps(this.store.getState()))
                });
            }
        	componentWillUnmount() {
                // 当组件删除的时候取消监听state的变化
                this.unsubscribe();
            }
        	render() {
                let actions = {};
                if(typeof mapDispatchToProps == 'function') {
                    actions = mapDispatchToProps(this.store.dispatch);
                } else if(mapDispatchToProps == 'object') {
                    actions = bindActionCreators(mapDispatchToProps, this.store.dispatch)
                }
                reurn <WrapedComponent {...this.state} {...actions}>
            }
        }
 		return ProxyComponent;
    }
}
```

# Redux的高阶组件和高阶函数

`Higher-Order Components` `HOC 高阶组件`：是一个函数，能够接收一个组件并返回一个新的组件，例如`react-redux`中的`connect`, `react-router-dom`中的`withRouter`，如下例子所示：

```javascript
class TestInput extends Component {
    render() {
        return <input {...this.props}>
    }
}
```

```javascript
// 属性代理
function addPropHOC(WrappedComponent) {
    return class PP extends Component {
        constructor(props) {
            super(props);
            this.state = { value: '' };
        }
        // 提取state控制交互数据
        onChange(event) {
            this.setState({ value: event.target.value });
        }
        
        // 生命周期处理其它内容
        componentWillMount() {
            
        }
        
        render() {
            const newProps = {
                value: this.state.value,
                onChange: this.onChange
            }
            // 其它元素包裹，布局改动
            return (
                <div>
                    <label>label:</label>
                    <!-- props属性代理，附加其它属性传递 -->
                	<WrappedComponent {...this.props} {...newProps} />
                </div>
            )
        }
    }
}

addPropHOC(TestInput);
```

```javascript
// 继承反转，实现组件同名方法，将覆盖原组件方法，如果调用需要用super，真正实现劫持，具有较强的侵入性
function extHOC(WrappedComponent) {
    return class Example extends WrappedComponent {
        componentWillMount() {
            super.componentWillMount();
        }
        render() {
            return super.render();
        }
    }
}

extHOC(TestInput);
```

`高阶函数`：参数是函数或者返回值是函数（`filter, map, addEventListener`）

# React的hooks，具体有useState, useEffect, useContext等的实现

`react`核心是组件，`v16.8`之前，组件的标准写法是`class`，以下一个简单按钮点击修改文本`demo`

```javascript
import React, { Component } from 'react';

export default class Button extends Component {
    constructor() {
        super();
        this.state = { buttonText: 'click!!!' };
        this.handleClick = this.handleClick.bind(this);
    }
    handleClick() {
        this.setState({ buttonText: 'been clicked.' });
    }
    render() {
        const { buttonText } = this.state;
        return <button onClick={this.handleClick}>{buttonText}</button>
    }
}
```

## React Hooks

可以在不编写`class`的情况下使用`state`和其它`react`特性，不能在`class`组件中使用，如果需要外部功能以钩子的方式添加使用，钩子一律使用`use`前缀标识，需要使用`xxx`功能，钩子命名为`usexxx`，默认提供的四个常用钩子`useState, useContext, useReducer, useEffect`，`Hook`是一种复用状态逻辑，不复用本身`state`，每次调用都有一个完全独立的`state`

## useState(initState): array

状态钩子，用于函数组件引入状态state，纯函数不能有状态，`useState(initState)` 接受状态初始值，作为参数，返回一个数组，数组第一个成员是变量，指向当前状态值，第二个成员是一个函数，用来更新状态，约定是`set`前缀加状态变量名

```javascript
import React, { useState } from 'react';

export default function Button() {
    const [ buttonText, setButtonText ] = useState('click!!!');
    function handleClick() {
        return setButtonText('been clicked.');
    }
    return <button onClick={handleClick}>{buttonText}</button>
}
```

## useContext(): object

共享状态钩子，如果需要在组件之间共享状态，可以使用`useContext()`

```javascript
// 创建一个context
const AppContext = React.createContext({});
<AppContext.Provider value={{username: 'superawesome'}}>
    <div className="app">
        <Navbar />
        <Messages />
    </div>
</AppContext.Provider>

const Navbar = () => {
    const { username } = useContext(AppContext);
    return <p>{username}</p>
}
```

## useReducer(reducer, initState): array

引入`reducer`功能，接受`reducer`函数和初始状态作为参数，返回数组，第一个成员是当前状态值，第二个成员是发送`action`的`dispatch`函数

```javascript
const myReducer = (state, action) => {
    switch(action.type) {
        case 'countUp':
            return {
                ...state, 
                count: state.count
            }
        default:
            return state;
    }
}

function AddCountButton() {
    const [state, dispatch] = useReducer(myReducer, { count: 0 });
    return <button onClick={() => dispatch({ type: 'countUp' })}>{state.count}</button>
}
```

## useEffect()

副作用钩子，接受两个参数，第一个参数是函数，第二个参数是一个数组，用于给出Effect的依赖项，只要这个数组发生变化，`useEffect`就会执行，第二个参数可以忽略，每次组件渲染，就会执行`useEffect`，相当于 `componentDidMount` 和 `componentDidUpdate`

```javascript
const Person = ({ personId }) => {
    const [loading, setLoading] = useState(true);
    const [person, setPerson] = useState({});
    
    useEffect(() => {
        setLoading(true);
        fetch(`url/${personId}`)
        	.then(response => response.json())
        	.then(data => {
				setPerson(data);
            	 setLoading(false);
            })
    }), [personId];
    
    if (loading === true) {
        return <p>Loading...</p>;
    }
    
    return <p>{JSON.stringify(person)}</p>;
}
```

## 自定义Hooks

封装一个自定义Hook

```javascript
const usePerson = (personId) => {
    const [ loading, setLoading ] = useState(true);
    const [ person, setPerson ] = useState({});
    
    useEffect(() => {
        setLoading(true);
        fetch(`url/${personId}`)
        	.then(response => response.json())
        	.then(data => {
				setPerson(data);
            	 setLoading(false);
            })
    }, [personId]);
    return [ loading, person ];
}

const Person = ({ personId }) => {
    const [loading, person] = usePerson(personId);
    if (loading === true) {
        return <p>Loading...</p>;
    }
    return <p>{JSON.stringify(person)}</p>;
}
```

```javascript
// 订阅好友的在线状态
function useFriendStatus(friendID) {
    const [isOnline, setIsOnline] = useState(null);
    function handleStatusChange(status) {
        setIsOnline(status.inOnline);
    }
    useEffect(() => {
        ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
        return () => {
            ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
        }
    });
}

function FriendStatus(props) {
    const isOnline = useFriendStatus(props.friend.id);
    if (isOnline === null) {
        return 'Loadin....';
    }
    return isOnline ? 'Online' : 'Offline';
}

function FriendListItem(props) {
    const isOnline = useFriendStatus(props.friend.id);
    return (
    	<li style={{ color: isOnline ? 'green' : 'black' }}>
        	{props.friend.name}
        </li>
    );
}
```



# React生命周期

生命周期状态：

- `Mounting`    挂载，已插入真实DOM
  - `constructor(props)`    如果不初始化state或不进行方法绑定，可以不实现构造函数，需要先调用`super(props)`
  - `static getDerivedStateFromProps(props, state)`    在render之前调用，并且在初始化及后续更新都会被调用，返回一个对象来更新state，如果返回null则不更新任何内容
  - ~~`componentWillMount`~~即将过时
  - `render()`
  - `componentDidMount()`    组件挂载后调用
- `Updating`    更新，正在被重新渲染，当props或state发送变化触发
  - `static getDerivedStateFromProps()`
  - ~~`componentWillUpdate,   componentWillReceiveProps`~~ 即将过时
  - `shouldComponentUpdate(nextProps, nextState)`    当`props`或`state`发生变化，渲染之前被调用，返回值默认`true`，首次渲染或者调用`forceUpdate()`不会调用该方法，返回`false`不更新，不调用`componentWillUpdate, render, componentDidUpdate`，但不会阻止子组件`state`更新时重新渲染
  - `render()`
  - `getSnapshotBeforeUpdate()`    在最近一次渲染输出到DOM之前调用，使得组件能在发生更改之前从DOM中捕获一些信息（例如：滚动位置），返回值将作为参数传递给`componentDidUpdate`
  - `componentDidUpdate(prevProps, prevState, snapshot)`    更新后调用，首次渲染不会调用，如果`shouldComponentUpdate返回false，则不会调用`
- `Unmounting`    卸载，已移除真实DOM
  - `componentWillUnmount()`    组件卸载销毁之前调用，清除timer、网络请求等资源
- Error    错误处理，渲染过程、生命周期、子组件构造函数中抛出错误
  - `static getDerivedStateFromError()`
  - `componentDidCatch(error, info)`

```javascript
class ScrollingList extends React.Component {
  constructor(props) {
    super(props);
    this.listRef = React.createRef();
  }

  getSnapshotBeforeUpdate(prevProps, prevState) {
    // 我们是否在 list 中添加新的 items ？
    // 捕获滚动位置以便我们稍后调整滚动位置。
    if (prevProps.list.length < this.props.list.length) {
      const list = this.listRef.current;
      return list.scrollHeight - list.scrollTop;
    }
    return null;
  }

  componentDidUpdate(prevProps, prevState, snapshot) {
    // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
    // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
    //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
    if (snapshot !== null) {
      const list = this.listRef.current;
      list.scrollTop = list.scrollHeight - snapshot;
    }
  }

  render() {
    return (
      <div ref={this.listRef}>{/* ...contents... */}</div>
    );
  }
}

// 重点是从 getSnapshotBeforeUpdate 读取 scrollHeight 属性，因为 “render” 阶段生命周期（如 render）和 “commit” 阶段生命周期（如 getSnapshotBeforeUpdate 和 componentDidUpdate）之间可能存在延迟。
```



# PureComponent 与 Component

内容完全相同，`PureComponent`通过`props`和`state`的浅对比来实现`shouldComponentUpdate`，所以不允许重写`shouldComponentUpdate`，如果是比较复杂的数据结构，会因深层的数据不一致而产生错误的否定判断，导致页面得不到更新

# React中refs的作用

可以使用`refs`来访问`dom`，做一些动画交互、媒体控件的播放，获取焦点、文本等

在组件挂载时候，会调用ref回调函数并传入`DOM`元素，卸载时调用并传入`null`，在`componentDidMount`或`componentDidUpdate`触发前，react会保证refs一定是最新的