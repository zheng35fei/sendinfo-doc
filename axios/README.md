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


