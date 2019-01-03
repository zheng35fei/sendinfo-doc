## new 一个Router

在init.js中实例化最外部的Vue, 
```js
import App from 'sendinfo-admin-ui/src/App.vue';
new Vue({
    el: '#app',
    store,
    router,
    components: {APP},
    template: '<APP/>'
})
```