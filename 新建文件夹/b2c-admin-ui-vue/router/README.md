## 路由 router
配置对应路径打开的页面和使用的模板文件，控制页面是否有权限进入，配置页面`title`等一些自定义参数。

在init.js中实例化根Vue;
```js
import App from 'sendinfo-admin-ui/src/App.vue';
import routerFunction from 'sendinfo-admin-ui/src/router';
//初始化路由
let router = routerFunction(store,route);
new Vue({
    el: '#app',
    store,      // 状态管理器
    router,     // 路由
    components: {APP},  // 父组件
    template: '<APP/>'  // 模板
})
```

## 路由定义
通过getmenu接口获取到的左侧和顶部的动态数据中的`funUrl`会被动态添加到路由当中；   
如果你需要添加不存在侧边栏的页面时，可以在/src/router/index.js中添加路由信息。

node_modules/src/router/router.js:
```js
import main from 'sendinfo-admin-ui/src/views/main.vue';
const otherRouter = {
        path: '/',
        name: 'home',
        redirect: '/index',
        component: main,
        children: [{
           path: '/index', title: '主页', name: 'home_index',
           meta: {
               title: '主页'
           },
           component: resolve => {require(['sendinfo-admin-ui/src/views/home/home.vue'], resolve);}
       },
       {
           path: '/userinfo', name: 'userinfo',
           meta: {
               title: '个人中心',
               cacheName:'userinfo-center'
           },
           component: resolve => require(['sendinfo-admin-ui/src/views/home/userinfo-center'], resolve)
       },
   ]
};

export const routers = [
    otherRouter // otherRouter: otherRouter 相同可简写
]
```
?> path: 路由路径   
   name: 路由名称，路由跳转可以直接使用name   
   redirect: 路由进入到/login后会重定向到这里定义的新路由  
   children: 子路由；  
   meta: 路由元信息， 通常用来标记页面是否需要登录，页面的标题等一些自定义信息  
   component: 路由需要加载的页面模板  

### 动态路由匹配

```js
const User = {
  template: '<div>User</div>'
}
const router = new VueRouter({
    routes: [
        {
           path: '/userinfo/:id', 
           component: User
        }
    ]
})

```

|模式	|匹配路径	|$route.params|
|---|---|---|
|/user/:username	|/user/evan	|{ username: 'evan' }|
|/user/:username/post/:post_id	|/user/evan/post/123	|{ username: 'evan', post_id: '123' }|

除了 `$route.params` 外，`$route` 对象还提供了其它有用的信息，例如，`$route.query` (如果 URL 中有查询参数)、`$route.hash` 等等。你可以查看 [API 文档](https://router.vuejs.org/zh/api/#%E8%B7%AF%E7%94%B1%E5%AF%B9%E8%B1%A1) 的详细说明。

[高级匹配模式](https://github.com/vuejs/vue-router/blob/next/examples/route-matching/app.js)

#### 路由匹配优先级
有时候，同一个路径可以匹配多个路由，此时，匹配的优先级就按照路由的定义顺序：谁先定义的，谁的优先级就最高。


## 创建router实例
在/src/router/index.js中调用上面配置的路由
```js
import {routers} from './router';

export default (store,routeConfig={})=>{
    let routes = [
        ...routers,
        ...store.getters.getRoutes, // 状态管理器中的路由 /src/store/modules/user.js;
        ...customRouters            // 新项目外部传入新加的自定义路由
    ]
    
    const router = new VueRouter({
        ...config,
        routes: routes
    });
    
    return router;
}
```


## 路由守卫
控制目标页面是否有权限进入，没有权限可以重定向到对应错误页

### 全局前置守卫 beforeEach
```js
// routeConfig 新项目自定义的新路由
// store：外部传入状态管理器存储的数据，token、用户信息等内容；
// 在进入路由前判断用户是否已经登录拿取到了token 或者是 不需要登录的页面，验证通过后,next()进入到对应页面

router.beforeEach((to, from, next) => {
    NProgress.start() // 开启Progress
    
    const token = store.state.user.tokenInfo.token;
    //如果用户已经登录并且访问的页面又是登录页面则直接跳转到首页
    if(token && to.path === '/login'){
        next({
            path: '/',
        })
        NProgress.done() // 关闭Progress
        return;
    }
    //如果直接是公开的 则直接就 next
    if(to.matched.some(record => record.meta.notRequire)){
        next();
    }else{
        routerJump(to,token,next)
    }
})
```

### 全局后置钩子 afterEach
```js
router.afterEach(() => {
    NProgress.done() // 关闭Progress
})
```

每个守卫方法接收三个参数：

* to: Route: 即将要进入的目标 路由对象

* from: Route: 当前导航正要离开的路由

* next: Function: 一定要调用该方法来 resolve 这个钩子。执行效果依赖 next 方法的调用参数。

    * next(): 进行管道中的下一个钩子。如果全部钩子执行完了，则导航的状态就是 confirmed (确认的)。
    
    * next(false): 中断当前的导航。如果浏览器的 URL 改变了 (可能是用户手动或者浏览器后退按钮)，那么 URL 地址会重置到 from 路由对应的地址。
    
    * next('/') 或者 next({ path: '/' }): 跳转到一个不同的地址。当前的导航被中断，然后进行一个新的导航。你可以向 next 传递任意位置对象，且允许设置诸如 replace: true、name: 'home' 之类的选项以及任何用在 router-link 的 to prop 或 router.push 中的选项。
    
    * next(error): (2.4.0+) 如果传入 next 的参数是一个 Error 实例，则导航会被终止且该错误会被传递给 router.onError() 注册过的回调。

*确保要调用 next 方法，否则钩子就不会被 resolved。*

[官方vue Router 导航守卫文档](https://router.vuejs.org/zh/guide/advanced/navigation-guards.html)


### 路由独享的守卫
在路由配置上定义`beforeEnter`
```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
```

### 组件内的守卫
在路由组件内直接定义一下路由导航守卫：

* beforeRouteEnter
* beforeRouteUpdate (2.2 新增)
* beforeRouteLeave

```js
const Foo = {
  template: `...`,
  beforeRouteEnter (to, from, next) {
    // 在渲染该组件的对应路由被 confirm 前调用
    // 不！能！获取组件实例 `this`
    // 因为当守卫执行前，组件实例还没被创建
  },
  beforeRouteUpdate (to, from, next) {
    // 在当前路由改变，但是该组件被复用时调用
    // 举例来说，对于一个带有动态参数的路径 /foo/:id，在 /foo/1 和 /foo/2 之间跳转的时候，
    // 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
    // 可以访问组件实例 `this`
  },
  beforeRouteLeave (to, from, next) {
    // 导航离开该组件的对应路由时调用
    // 可以访问组件实例 `this`
  }
}
```

## 页面左侧菜单的子路由生成
!> 左侧菜单栏目的路由由状态管理器部分的`/src/store/modules/user.js`中的*getRouteByMenu*方法动态生成

!> 在登录页面登录成功后会把菜单数据插入到store中，store内的user.js，getter内方法会根据数据动态生成路由。
/src/store/modules/user.js: 
```js
getters: {
    getTreeMenu: (state) => {
        getMenuList(state.userinfo.menuList);
        function getMenuList(list){
            list.forEach(item => {
                if(typeof item.id === 'number'){
                    item.id = item.id.toString()
                }
                if(item.list && item.list.length > 0){
                    getMenuList(item.list)
                }
            })
        }
        localStorage.set('userinfo', state.userinfo);
        return state.userinfo.menuList
    },
    getRoutes: (state) => {
        let routes = [];
        if (state.userinfo.menuList && state.userinfo.menuList.length > 0) {
            buildMenuRoutes(state.userinfo.menuList)
        }else{
            routes = [];
        }
        function buildMenuRoutes(list){
            list.forEach(menu => {
                if(menu.funUrl !== ''){
                    routes.push(getRouteByMenu(menu));
                }else{
                    if(menu.list && menu.list.length > 0){
                        buildMenuRoutes(menu.list)
                    }
                }
            })
        }
        return addMainToRoutes(routes);
    }
}
```

##  Vue 实例内部页面使用
```html
<router-link :to="{ path: '/login', params: { redirectUrl: this.$route.fullPath }}">login</router-link>
```
或者使用name
```html
<router-link :to="{ name: 'login', params: { redirectUrl: this.$route.fullPath }}">login</router-link>
```
当你点击 `<router-link>` 时，这个方法会在内部调用，所以说，点击 `<router-link :to="...">` 等同于调用 `router.push(...)`。

在 Vue 实例内部，你可以通过 `$router` 访问路由实例。因此你可以调用 `this.$router.push`。  
这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL


|声明式	| 编程式|  
|--- | --- |
| `<router-link :to="..." replace>`	| `router.replace(...)`|

```js
this.$router.push({
    path:'/login',
    params: { redirectUrl: this.$route.fullPath }
})

// 官方例子
// 字符串
router.push('home')

// 对象
router.push({ path: 'home' })

// 命名的路由
router.push({ name: 'user', params: { userId: 123 }})

// 带查询参数，变成 /register?plan=private
router.push({ path: 'register', query: { plan: 'private' }})
```
!> 注意：如果提供了 `path`，`params` 会被忽略; 需要手写完整的带`params`的`path`
```js
const userId = 1;
router.push({ path: 'register/' + userId })
```

!> 注意：如果目的地和当前路由相同，只有参数发生了改变 (比如从一个用户资料到另一个 /users/1 -> /users/2)，你需要使用 [beforeRouteUpdate](https://router.vuejs.org/zh/guide/essentials/dynamic-matching.html#%E5%93%8D%E5%BA%94%E8%B7%AF%E7%94%B1%E5%8F%82%E6%95%B0%E7%9A%84%E5%8F%98%E5%8C%96) 来响应这个变化 (比如抓取用户信息)。
