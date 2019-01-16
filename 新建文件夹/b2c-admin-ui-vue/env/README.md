## 安装环境
1. 安装nodeJs  
[node下载地址](http://nodejs.cn/download/)

        安装完成cmd `node -v` 查看node版本   
        安装完成nodeJs后会附带npm, `npm -v` 查看npm版本

2. 进入项目目录，安装依赖包   
项目根目录下会有`package.json`：    
```json
{
    "dependencies": {
        "@chenfengyuan/vue-qrcode": "^1.0.0",
        ...
    },
    "devDependencies": {
        "autoprefixer": "^7.1.2",
        "babel-core": "^6.22.1",
        "babel-eslint": "^8.2.5",
        ...
    }
}
```
`dependencies`, `devDependencies`内的包都是通过`npm install` (简写 `npm i` )安装。    

    由于网络问题，直接npm安装可能会比较慢，可以安装淘宝NPM镜像来下载；  
    
?> 安装 `npm install -g cnpm --registry=https://registry.npm.taobao.org`   
** -g ** 全局安装   
安装成功以后可以使用`cnpm`替代`npm`使用

!> `sendinfo-admin-ui`包是在公司内部npm仓库中，所以也需要改仓库地址下载   
`npm config set registry http://sendinfo-cs-java.tpddns.cn:8081/repository/npm-group/`

## 运行项目
在项目根目录下：  
```
npm run dev
或者
npm start
```

实际运行的是`package.json`中的
```json
"scripts": {
    "dev": "webpack-dev-server --inline --progress --config build/webpack.dev.conf.js",
    "start": "npm run dev",
    "lint": "eslint --ext .js,.vue src",
    "test": "node build/test-build.js",
    "build": "node build/build.js"
}
```


?> 启动之前需要确认修改端口和接口地址

[下一节： 配置](/config)

