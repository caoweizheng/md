# webpack 3.x #
### webpack.config.js ###
- entry:{}     : 入口
	- entry: './src/entry.js'
	- ... 可以配置多个，名字不一样就行
- output:{}    : 出口
	- path: path.resolve(__dirname, 'dist')
	- filename: [name].js // 出口文件名， [name] 表示与入口名一致
- module:{}    : 模块
- plugins:[]   : 插件
- devServer:{} : 开发服务