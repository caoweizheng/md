- store.dispatch(action)
	- action是一个对象
- reducer
	- 返回新的state
- 安装中间件后，action可以是函数
		
		// store/index.js
		// 全局唯一的store
		// 下面是配置了中间件和redux-devtools
		import { createStore, applyMiddleware, compose } from 	'redux'
		import reducer from './reducer'
		import thunk from 'redux-thunk'
		
		const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ?
		  window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose
		
		const enhancer = composeEnhancers(applyMiddleware(thunk))
		
		const store = createStore(reducer, enhancer)
		
		export default store

		
		// store/actionCreators.js
		// 统一管理创建action
		import { CHANGE_VALUE, ADD_ITEM, DELETE_ITEM, QUERY_LIST } from "./actionTypes";

		export const changeValueAction = (value) => (
		  {
		    type: CHANGE_VALUE,
		    value
		  }
		)
		
		export const addItemAction = () => (
		  {
		    type: ADD_ITEM
		  }
		)
		
		export const deleteItemAction = (index) => (
		  {
		    type: DELETE_ITEM,
		    index
		  }
		)
		
		export const queryListAction = (data) => (
		  {
		    type: QUERY_LIST,
		    data
		  }
		)
		// 安装中间件可以返回函数
		export const middleQueryTodoList = () => {
		  return (dispatch) => {	
     		// 可以在这里发起请求
		    setTimeout(() => {
		      const data = [1,2,3,4]
		      const action = queryListAction(data) // 更新列表的action
		      dispatch(action)
		    }, 3000);
		  }
		} 

		// store/actionTypes.js
		// 统一管理action名称
		export const CHANGE_VALUE = 'changeValue'
		export const ADD_ITEM = 'addItem'
		export const DELETE_ITEM = 'deleteItem'
		export const QUERY_LIST = 'querylist'


		// stroe/reducer.js
		// 仓库管理员
		// 在此返回一个新的state, 返回给store来改变state
		import { CHANGE_VALUE, ADD_ITEM, DELETE_ITEM, QUERY_LIST } from './actionTypes'
		const defaultState = {
		  inputValue: 'doing something',
		  list: []
		}
		
		export default (state = defaultState, action) => {
		  let newState;
		  switch (action.type) {
		    case CHANGE_VALUE:
		      newState = JSON.parse(JSON.stringify(state))
		      newState.inputValue = action.value
		      return newState
		    case DELETE_ITEM:
		      newState = Object.assign({}, state)
		      newState.list.splice(action.index, 1)
		      return newState
		    case ADD_ITEM:
		      newState = Object.assign({}, state)
		      newState.list = [...state.list, state.inputValue]
		      newState.inputValue = ''
		      return newState
		    case QUERY_LIST:
		      newState = Object.assign({}, state)
		      newState.list = action.data
		      return newState
		    default:
		      return state
		  }
		}  

		// todolist.js
		// 页面使用调用store的方法管理仓库状态
		  handleChange (e) {
		    const action = changeValueAction(e.target.value)
		    store.dispatch(action)
		  }
		  storeChange () {
		    this.setState(store.getState())
		  }
		  removeItem = (e, index) => {
		    console.log(index)
		    const action = deleteItemAction(index)
		    store.dispatch(action)
		  }
		  addItem () {
		    const action = addItemAction()
		    store.dispatch(action)
		  }
		 // 配置了中间件这样使用
		  componentDidMount () {		
		    const action = middleQueryTodoList() // 中间件里处理查询数据逻辑
		    store.dispatch(action)
		  }





# ==================== #



- reducer
	- reducer 只是一个接收 state 和 action，并返回新的 state 的函数

- 容器组件和展示组件概念
- 
		展示组件	容器组件
		作用	描述如何展现（骨架、样式）	描述如何运行（数据获取、状态更新）
		直接使用 Redux	否	是
		数据来源	props	监听 Redux state
		数据修改	从 props 调用回调函数	向 Redux 派发 actions
		调用方式	手动	通常由 React Redux 生成

