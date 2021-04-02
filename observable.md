## observable ##
-  arr
	-  ObservableArray => likeArray
-  object
	-  ObservableObject => linkObject

## computed ##
- 将多个可观察数据组成一个可观察数据
- 接收一个无参的函数
- 返回store中的数据
- 调用observe函数,该函数接收一个change 参数的函数
- 当被引用的数据发生改变时,observe 函数会执行

## autorun ##
- 可自动追踪其引用的可观察数据,并在数据发生变化时重新触发
- autorun 接收一个午餐的函数
- 函数中引用store中的数据
- 当引用的数据发生改变时,该函数自动执行

## when ##
- 可根据条件...,autorun 的变种
- 该函数接收 两个参数
- 第一个 返回非普通boolean 值的函数,
- 第二个参数为 当第一个参数为true时, 将要执行的函数

## reaction ##
- 对autorun 做出改进,
- 该函数接收两个参数
- 第一个参数为 返回将要观察的数据的函数
- 第二个参数为 参数为第一个函数参数返回的值,观察数据发生改变时将执行的函数
- 应用场景
	- 在没有数据之前,我们不想调用写缓存的逻辑,可以用reaction 来操作,在数据被第一次填充之后,才执行写缓存的逻辑












## 组件中的间接引用值? ##
