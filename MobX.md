# mobx要点 -- 帮助你以一种简单直观的方式来完成工作#

## 1.定义状态并使其可观察 ##
- 可以用任何你喜欢的数据结构来存储状态，如对象、数组、类。 循环数据结构、引用，都没有关系。 只要确保所有会随时间流逝而改变的属性打上 mobx 的标记使它们变得可观察即可

		import {observable} from 'mobx';
		
		var appState = observable({
		    timer: 0
		});
## 2.创建视图以相应视图的变化 ##
- 目前appState 还没有观察到任何的东西。 你可以创建视图，当 appState 中相关数据发生改变时视图会自动更新。 MobX 会以一种最小限度的方式来更新视图

		import {observer} from 'mobx-react';
		
		@observer
		class TimerView extends React.Component {
		    render() {
		        return (<button onClick={this.onReset.bind(this)}>
		                Seconds passed: {this.props.appState.timer}
		            </button>);
		    }
		
		    onReset () {
		        this.props.appState.resetTimer();
		    }
		};
		
		ReactDOM.render(<TimerView appState={appState} />, document.body);
## 3.更改状态 ##
- 下面的代码每秒都会修改你的数据，而当需要的时候UI会自动更新。 无论是在改变状态的控制器函数中，还是在应该更新的视图中，都没有明确的关系定义。 使用 observable 来装饰你的状态和视图，这足以让 MobX检测所有关系了

		appState.resetTimer = action(function reset() {
		    appState.timer = 0;
		});
		
		setInterval(action(function tick() {
		    appState.timer += 1;
		}), 1000);

- 只有在严格模式(默认是不启用)下使用 MobX 时才需要 action 包装。 建议使用 action，因为它将帮助你更好地组织应用，并表达出一个函数修改状态的意图。 同时,它还自动应用事务以获得最佳性能

# 概念与原则 #
- State(状态)
	- 状态 是驱动应用的数据。 通常有像待办事项列表这样的领域特定状态，还有像当前已选元素的视图状态。 记住，状态就像是有数据的excel表格
- Derivations(衍生)
- 任何 源自状态并且不会再有任何进一步的相互作用的东西就是衍生。 衍生以多种形式存在:
	- 用户界面
	- 衍生数据，比如剩下的待办事项的数量。
	- 后端集成，比如把变化发送到服务器端。
