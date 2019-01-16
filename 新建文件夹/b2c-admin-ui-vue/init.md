## 初始化vue实例

```js
Vue.prototype.util = util;

// 加载自定义组件
Vue.use(customComponents)

/*一些配置信息*/
let defaultGateway = process.env.DEFAULT_GATEWAY ?`/${process.env.DEFAULT_GATEWAY}`:''
Vue.prototype.setting = {
    DOMAIN: process.env.BASE_URL,
    VERIFY_CODE_URL:`${process.env.BASE_URL}${defaultGateway}/common/code/refreshCode`,
    QRCODE_PATH: `${process.env.BASE_URL}${defaultGateway}/common/code/QRCodeGenerate`,
    BARCODE_PATH: `${process.env.BASE_URL}${defaultGateway}/common/code/barCodeGenerate`,
    FILE_URL: `${process.env.BASE_URL}${defaultGateway}/images`,
    FILE_TEMP_URL: `${process.env.BASE_URL}${defaultGateway}/img`,
    FILE_MEDIA_URL: `${process.env.BASE_URL}${defaultGateway}/media`,
    UPLOAD_URL: process.env.BASE_URL +defaultGateway+ constant.UPLOAD_URI,
    UPLOAD_TEMP_URL: process.env.BASE_URL +defaultGateway+ constant.UPLOAD_TEMP_URI,
    UPLOAD_MEDIA_URI: process.env.BASE_URL +defaultGateway+ constant.UPLOAD_MEDIA_URI,
    MULTIPLE_UPLOAD_URL: process.env.BASE_URL +defaultGateway+ constant.MULTIPLE_UPLOAD_URI
};

//初始化sessionid
store.commit('INIT_SESSIONID');

export default (config)=>{
    config = config || {};
    let route = config.route || {};
    //初始化路由
    let router = routerFunction(store,route);
    //初始化 axios
    const axios = axiosInit(store,router,config.axiosConfig);
    let elementUIConfig = config.elementUIConfig || {};// { size: 'small', zIndex: 3000 }
    if(!elementUIConfig.zIndex){
        elementUIConfig.zIndex = 1000
    }
    Vue.use(ElementUI, elementUIConfig);
    Vue.use(Avue,axios)
    //加载自定义的 插件
    Vue.use(plugins)

    new Vue({
        el: config.el || '#app',
        store,
        router,
        components: config.component || {App},
        template: config.template || '<App/>',
    });
}
```

## 使用插件

通过全局方法 Vue.use() 使用插件。它需要在你调用 new Vue() 启动应用之前完成：

```js
Vue.use(customComponents)
```
也可以传入一个选项对象：
```js
Vue.use(ElementUI, elementUIConfig);
Vue.use(Avue,axios)

new Vue({
  //... options
})
```

