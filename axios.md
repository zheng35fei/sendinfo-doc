## axios 
ajxa请求接口的插件。   
* 从浏览器中创建 XMLHttpRequests
* 从 node.js 创建 http 请求
* 支持 Promise API
* 拦截请求和响应
* 转换请求数据和响应数据
* 取消请求
* 自动转换 JSON 数据
* 客户端支持防御 XSRF

## 加载axios

/src/init.js:
```js
Vue.use(Avue,axios)
```

## 配置 config
/src/core/axios/indexAxios.js
可以添加一些全局的设置，如默认接口地址、超时时间
```js
axios.defaults.baseURL = process.env.BASE_URL +'/'+ process.env.DEFAULT_GATEWAY;
axios.defaults.timeout = 60000;
```
也可以添加headers内的content-type,是每次请求是都带上Token
axios.defaults.headers. = 60000;


## 拦截器 interceptors
```js
// 添加一个请求拦截器
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// 添加一个响应拦截器
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });
```

每次请求接口在headers里添加token；也可以加自定义属性
/src/core/axios/index.js:
```js
// 添加一个请求拦截器
axios.interceptors.request.use(function (config) {
    if(!store.getters.sessionId){
        store.commit('INIT_SESSIONID');
    }
    config.headers.common = {
        ...config.headers.common,
        SESSIONID:store.getters.sessionId
    }
    if(store.state.user.tokenInfo){
        let token = store.state.user.tokenInfo.token;
        if (token) {
            const header = util.getTokenHeader(token);
            config.headers.common={
                ...config.headers.common,
                ...header
            }
        }
    }
    return config;
}, function (error) {
    // Do something with request error
    return Promise.reject(error);
});
```

## 使用
/src/core/axios/vueAxiosExt.js
将axios挂载到vue实例中；
```js
Vue.axios = axios
Object.defineProperties(Vue.prototype, {
    axios: {
        get() {
            return axios
        }
    },

    $http: {
        get() {
            return axios
        }
    }
})
```

实例中可通过`this.$http` 或 `this.axios` 拿到axios对象；
```js
// get
this.$http.get(url, {params, headers}).then( res => {})

// post
this.$http.post(url, params).then( res => {})
```

axios返回的是一个promise对象，也可以:
```js
methdos: {
    async login(){
        const { data } = await this.$http.post(url, data);
        console.log(data)
    }
}
```
异步的过程使用async和await可以变成同步流程执行，等待接口返回数据后再走到下一步；  
如果有多个接口没有依赖关系,并发发起也可以使用axios.all：
```js
function getUserAccount() {
  return axios.get('/user/12345');
}

function getUserPermissions() {
  return axios.get('/user/12345/permissions');
}

axios.all([getUserAccount(), getUserPermissions()])
.then(axios.spread(function (acct, perms) {
console.log('两个请求都完成了')
}));
  
```


