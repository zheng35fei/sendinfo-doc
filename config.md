## 配置文件 config

### 环境参数配置文件
项目接口地址、网关、环境、项目名称等配置   
/config/dev.env.js  
/config/prod.env.js  
/config/test.env.js  

```js
{
    NODE_ENV: '"development"',
    PROJECT_NAME: '"电商平台"',
    BASE_URL: '"http://192.168.18.112:8080"',
    DEFAULT_GATEWAY:'"admin"',
    // 项目名称存入状态管理器vuex store
    website:()=>{
        return {
            title:'江南温泉'
        }
    }
}
```

### webpack基础配置 /config/index.js:
```js
module.exports = {
    // 开发模式配置
    dev: {
        // 静态文件路径配置
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
        
        // 代理配置，如果接口不支持跨域，需要通过代理转发请求。
        // 代理启动一个node服务，转发请求。
        // /api/v3在正常接口前存在有这个路径都会通过代理, /api/v3可以自定义修改
        proxyTable: {
            '/api/v3': {
                target: 'http://192.168.200.174:8080',
                secure: false,      // 默认情况下，不接受运行在 HTTPS 上，且使用了无效证书的后端服务器。如果你想要接受
                changeOrigin: true, // 是否跨域
                pathRewrite: {      // 路径重写 *^/api/v3*被重写成''，变成正确接口路径
                    '^/api/v3': ''
                }
            }
        },
        //可以简写成
        proxyTable: {
          "/api/v3": "http://192.168.200.174:8080"
        },
        
        // Various Dev Server settings
        //'localhost'运行无法使用ip访问,可使用0.0.0.0
        host: 'localhost', // can be overwritten by process.env.HOST; 
        port: 8081, // can be overwritten by process.env.PORT, if port is in use, a free one will be determined
        autoOpenBrowser: true,
        errorOverlay: true,
        notifyOnErrors: true,
        poll: false, // https://webpack.js.org/configuration/dev-server/#devserver-watchoptions-
    
        // 是否开启esLint代码规范
        useEslint: true,
        
        // https://webpack.js.org/configuration/devtool/#development
        devtool: 'source-map',
    },    
    // 生产模式webpack编译用配置
    build: {
        // Template for index.html
        // 编译生成首页index.html路径
        index: path.resolve(__dirname, '../dist/index.html'),
    
        // Paths
        // 静态文件编译生成路径
        assetsRoot: path.resolve(__dirname, '../dist'),
        assetsSubDirectory: 'static',
        assetsPublicPath: '/',
    }   
}
```

### Vue.js devtools插件(webkit内核和Firefox可安装)  
[chrome网上应用地址](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd)  
![devTool](/imgs/devTool.png)


## webpack配置
webpack.base.conf.js  
webpack.base.dev.js  
webpack.base.prod.js  
webpack.base.test.js  

开发项目启动命令 *npm start* 或者 *npm run dev*；使用webpack.base.dev.js 配置
/package.json:

````js
"scripts": {
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",                 // 等同npm run dev
    "lint": "eslint --ext .js,.vue src",    // 检查是否符合esLint规范
    "test": "node build/test-build.js",     // 使用测试配置文件进行编译
    "build": "node build/build.js"          // 使用生产配置编译项目
},
````



