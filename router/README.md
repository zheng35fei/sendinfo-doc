## new 一个Router

在init.js中实例化根Vue;
```js
import App from 'sendinfo-admin-ui/src/App.vue';
import routerFunction from 'sendinfo-admin-ui/src/router';
//初始化路由
let router = routerFunction(store,route);
new Vue({
    el: '#app',
    store,
    router,
    components: {APP},
    template: '<APP/>'
})
```

