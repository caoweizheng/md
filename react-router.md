## react-router ##
 - import { BrowserRouter as Router, Route, Link } from "react-router-dom";
 		

		<Router>
	      <ul>
	        <li>
	          <Link to="/">Tab1</Link>
	        </li>
	        <li>
	          <Link to="/tab2">Tab2</Link>
	        </li>
	      </ul>
	      <Route path="/" exact component={Tab1}></Route>
	      <Route path="/tab2" component={Tab2}></Route>
	    </Router>
		 exact: 精确匹配，路径完全一致才能匹配上，非精确匹配，只要包含设置的路径就能匹配上。

		// 路由传参
		// 设置规则
		<Route path="/tab2/:id" component={Tab2}></Route>
		// 传递参数
		<Link to="/tab2/123">Tab2</Link>
		// 接收参数
		this.props.match.params.id