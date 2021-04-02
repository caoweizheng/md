- 优先与global.css 加载样式，不需要引入，可以在项目的任何地方使用到

		// vue.config.js
		module.exports = {
		  css: {
		    loaderOptions: {
		      sass: {
		        prependData: `
		        @import '@/styles/variable.scss';
		        @import '@/styles/mixins.scss';
		        `,
		      },
		    },
		  },
		}

- 伪进度条
		import NProgress from 'nprogress';
		
		router.beforeEach(() => {
		  NProgress.start();
		});
		
		router.afterEach(() => {
		  NProgress.done();
		});

- 美化滚动条

		::-webkit-scrollbar {
		  width: 6px;
		  height: 6px;
		}
		
		::-webkit-scrollbar-track {
		  width: 6px;
		  background: rgba(#101F1C, 0.1);
		  -webkit-border-radius: 2em;
		  -moz-border-radius: 2em;
		  border-radius: 2em;
		}
		
		::-webkit-scrollbar-thumb {
		  background-color: rgba(#101F1C, 0.5);
		  background-clip: padding-box;
		  min-height: 28px;
		  -webkit-border-radius: 2em;
		  -moz-border-radius: 2em;
		  border-radius: 2em;
		}
		
		::-webkit-scrollbar-thumb:hover {
		  background-color: rgba(#101F1C, 1);
		}

- 100vh 问题

		vh-check npm install vh-check \--save
		import vhCheck from 'vh-check';
		vhCheck('browser-address-bar');
		// 定义一个mixinscss
		@mixin vh($height: 100vh) {
		  height: $height;
		  height: calc(#{$height} - var(--browser-address-bar, 0px));
		}

- request.js

		import axios from 'axios';
		import get from 'lodash/get';
		// store 作为本地储存的工具用来存储 token
		import storage from 'store';
		// 创建 axios 实例
		const request = axios.create({
		 // API 请求的默认前缀
		 baseURL: process.env.VUE_APP_BASE_URL,
		 timeout: 10000, // 请求超时时间
		});
		
		// 异常拦截处理器
		const errorHandler = (error) => {
		 const status = get(error, 'response.status');
		 switch (status) {
		   /* eslint-disable no-param-reassign */
		   case 400: error.message = '请求错误'; break;
		   case 401: error.message = '未授权，请登录'; break;
		   case 403: error.message = '拒绝访问'; break;
		   case 404: error.message = `请求地址出错: ${error.response.config.url}`; break;
		   case 408: error.message = '请求超时'; break;
		   case 500: error.message = '服务器内部错误'; break;
		   case 501: error.message = '服务未实现'; break;
		   case 502: error.message = '网关错误'; break;
		   case 503: error.message = '服务不可用'; break;
		   case 504: error.message = '网关超时'; break;
		   case 505: error.message = 'HTTP版本不受支持'; break;
		   default: break;
		   /* eslint-disabled */
		 }
		 return Promise.reject(error);
		};
		
		// request interceptor
		request.interceptors.request.use((config) => {
		 // 如果 token 存在
		 // 让每个请求携带自定义 token 请根据实际情况自行修改
		 // eslint-disable-next-line no-param-reassign
		 config.headers.Authorization = `bearer ${storage.get('ACCESS_TOKEN')}`;
		 return config;
		}, errorHandler);
		
		// response interceptor
		request.interceptors.response.use((response) => {
		 const dataAxios = response.data;
		 // 这个状态码是和后端约定的
		 const { code } = dataAxios;
		 // 根据 code 进行判断
		 if (code === undefined) {
		   // 如果没有 code 代表这不是项目后端开发的接口
		   return dataAxios;
		 // eslint-disable-next-line no-else-return
		 } else {
		   // 有 code 代表这是一个后端接口 可以进行进一步的判断
		   switch (code) {
		     case 200:
		       // [ 示例 ] code === 200 代表没有错误
		       return dataAxios.data;
		     case 'xxx':
		       // [ 示例 ] 其它和后台约定的 code
		       return 'xxx';
		     default:
		       // 不是正确的 code
		       return '不是正确的code';
		   }
		 }
		}, errorHandler);
		
		export default request;

- mock.js

		import Mock from 'mockjs';
	
		Mock.setup({
		  timeout: '500-800',
		});
		
		const context = require.context('./services', true, /\.mock.js$/);
		
		context.keys().forEach((key) => {
		  Object.keys(context(key)).forEach((paramKey) => {
		    Mock.mock(...context(key)[paramKey]);
		  });
		});

		示例模板
		
		import Mock from 'mockjs';
		
		const { Random } = Mock;
		
		export default [
		  RegExp('/example.*'),
		  'get',
		  {
		    'range|50-100': 50,
		    'data|10': [
		      {
		        // 唯一 ID
		        id: '@guid()',
		        // 生成一个中文名字
		        cname: '@cname()',
		        // 生成一个 url
		        url: '@url()',
		        // 生成一个地址
		        county: Mock.mock('@county(true)'),
		        // 从数组中随机选择一个值
		        'array|1': ['A', 'B', 'C', 'D', 'E'],
		        // 随机生成一个时间
		        time: '@datetime()',
		        // 生成一张图片
		        image: Random.dataImage('200x100', 'Mock Image'),
		      },
		    ],
		  },
		];
modules
每个页面模块或页面的子模块添加属性 namespaced: true。
vuepress 构建文档