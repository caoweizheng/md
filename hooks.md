## useState 
```
const [state, setState] = useState(initState)
```

- eg: useState(0), setState(prevState => prevState +1), state: {state}
- 返回一个 state，以及更新 state 的函数。


## useEffect
- componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合
- useEffect( () => {...todo}, [依赖])
 - 默认情况下，会在**每轮**组件渲染结束后执行
 - 有时为了防止内存泄漏，可以返回一个清除函数来取消订阅(例如计时器id)，清除函数会在组件卸载前执行。
  - 与 componentDidMount、componentDidUpdate 不同的是，Taro 会在 setData 完成之后的下一个 macrotask 执行 effect 的回调函数，传给 useEffect 的函数会延迟调用。这使得它适用于许多常见的副作用场景，比如如设置订阅和事件处理等情况，因此不应在函数中执行渲染和更新

 - 依赖数组中设置有值，会在该值发生改变是才会调用useEffect的回调函数
	 - 有时候不需要每次组件渲染后都执行回调是可以这样做
		``` 
		useEffect(() => {
		    const subscription = props.source.subscribe();
		    return () => {
		      subscription.unsubscribe();
		    };
		  },
		  [props.source],
		); 
		```
		- 此时，只有当 props.source 改变后才会重新创建订阅

## useReducer
- useState的替代方案
```
const [state, dispatch] = useReducer(reducer, initialState, init);
```
	- reducer: 改变state
	- initialState: state初始值
	- init: 可以用来重置state的值
			
	

- useReducer接收一个reduce函数和state的初始化值，返回当前的state以及配套的dispatch方法
- 调用dispatch方法来触发reducer函数
- 初始化state
	- 指定初始 state
	- 惰性初始化
	```
	function init (initialCount) {
	  return { count: initialCount };
	}
	function reducer (state, action) {
	  switch (action.type) {
	    case 'increment':
	      return { count: state.count + 1 };
	    case 'decrement':
	      return { count: state.count - 1 };
	    case 'reset':
	      return init(action.payload);
	    default:
	      throw new Error();
	  }
	}
	function Counter ({ initialCount }) {
	  const [state, dispatch] = useReducer(reducer, initialCount, init);
	  return (
	    <View>
	      Count: {state.count}
	      <Button
	        onClick={() => dispatch({ type: 'reset', payload: initialCount })}>
	        Reset
	  </Button>
	      <Button onClick={() => dispatch({ type: 'increment' })}>+</Button>
	      <Button onClick={() => dispatch({ type: 'decrement' })}>-</Button>
	    </View>
	  );
	}
	```

	
- 你可以选择惰性地创建初始 state。为此，需要将 init 函数作为 useReducer 的第三个参数传入，这样初始 state 将被设置为 init(initialArg)。
- 这么做可以将用于计算 state 的逻辑提取到 reducer 外部，这也为将来对重置 state 的 action 做处理提供了便利

## useContext & 配合useReducer实现共享状态
- const value = useContext(MyContext)
- 接收一个 context (Taro.createContext 的返回值）并返回该 context 的当前值。当前的 context 值由上层组件中最先渲染的 <MyContext.Provider value={value}> 的 value决定
- 可用于组件通信

```
// context.js
import { createContext, useReducer } from "@tarojs/taro"
const CountContext = createContext()
const ColorContext = createContext()
const UPDATE_COLOR = 'UPDATE_COLOR'
const reducer = (state, action) => {
  switch (action.type) {
    case 'UPDATE_COLOR':
      return action.color
    default:
      return state
  }
}
const Color = (props) => {
  const [color, dispatch] = useReducer(reducer, 'blue')
  return (<ColorContext.Provider value={{ color, dispatch }}>
    {props.children}
  </ColorContext.Provider>)
}
export { CountContext, ColorContext, Color, UPDATE_COLOR }
// buttons.js
import Taro, { useContext, useReducer } from '@tarojs/taro'
import { ColorContext, UPDATE_COLOR } from '../../context'
const Buttons = props => {
  const { dispatch } = useContext(ColorContext)
  return (<View >
    <Button onClick={() => dispatch({ type: 'UPDATE_COLOR', color: 'red' })}>红色</Button>
    <Button onClick={() => dispatch({ type: 'UPDATE_COLOR', color: 'yellow' })}>黄色</Button>
  </View>)
}
export default Buttons
// showArea.js
import Taro, { Component, useContext, useReducer } from '@tarojs/taro'
import { View } from "@tarojs/components"
import { ColorContext } from "../../context";
const ShowArea = props => {
  let { color } = useContext(ColorContext)
  return (<View style={{ color: color }} >当前颜色为: {color}</View>)
}
export default ShowArea
``` 

 - 0. 在context.js建立公共的context（通过createContext({})）然后暴露出去，其他组件想使用将其导进来就可以使用公共状态了（通过useContext()）。
 - 1. 用Context.provider将组件包起来。
 - 2. 在value将共享属性暴露出去
 - 3.配合userDeducer，value暴露出去的是dispatch，在其他组件可通过useContext获取这个dispatch， 然后调用从而改变reduser

 - tip： reducer返回一个state和dispatch，将这两个共享出去来，其他组件就可以获取到state和改变state（通过dispatch）

## useCallback
```
const memoizedCallback = useCallback(
  () => {
doSomething(a, b);
  },
  [a, b],
);
```


## useMemo
```
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

## useRef 
- ```
	const refContainer = useRef(initialValue);
  ```
	- ```
		<Input ref={inputEl} type="text" />
	 ```
