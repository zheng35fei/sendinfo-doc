# 目录
## 静态资源 static

> 在static/下的文件都不会被Webpack处理：它们使用相同的文件名，直接拷贝到最终的路径。
    你必须使用绝对路径来引用这些文件。
    config/index.js 内配置了是否是static文件夹，可以通过修改配置改变静态文件夹的名称和位置
    
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
-------------------
##### 区别于assets
>assets内文件会被webpack根据配置的loader重新处理。
如在*.vue组件中，所有的templates和css都会被vue-html-loader 和 css-loader解析。这些资源可能在构建的时候被内联/复制/重命名
如小于设置值大小的图片会被转成base64后内联在img的src中。最后build打包都会生成项目源码的一部分，
所有过大的部分图片或者不需要打包进项目的js插件不适于放于assets中,可以上传到CDN上引用使用。

>举个例子，在<img src="./logo.png"> 和 background: url(./logo.png), "./logo.png"中，都是相对资源路径，都会被Webpack解析成模块依赖 。

## 项目源码 src

### 静态资源 assets

资源处理规则
> 相对URL, e.g. ./assets/logo.png 将会被解释成一个模块依赖。它们会被一个基于你的Webpack输出配置自动生成的URL替代。

> 没有前缀的URL, e.g. assets/logo.png 将会被看成相对URL，并且转换成./assets/logo.png

> 前缀带~的URL e.g. ~assets/logo.png 会被当成模块请求, 类似于require('some-module/image.png'). 如果你想要利用Webpack的模块处理配置，就可以使用这个前缀。例如，如果你有一个对于assets的路径解析，你需要使用<img src="~assets/logo.png">来确保解析是对应上的。

> 相对根目录的URL, e.g. /assets/logo.png 是不会被处理的

### 组件 components
##### 自定义组件:
> areas,
jqgrid,
printJs,
remote-radio,
remoteSelect,
selectTree,
sticky,
tinymce,
treeTable,
UEditor,
upload-file,
vInput,
ztree

>./index.js:
 
    const components = [areas, ztree,...];
    const install = function(Vue) {
        components.may( component => {
            Vue.component(component.name. component);
        })
    }
> /src/init.js:
 
    加载自定义组件(加载到文件夹默认是内部的index.js)
    const components = require(./scr/components)
    Vue.use(components)
### core(axios, localStore, directives, filters, mixins, utils)
##### axios
> 异步请求接口的组件。默认返回promise对象；

> /src/init.js:
    Vue.use(axios)
    
[查看axios更多设置](/axios)

##### 浏览器缓存 localStore
> 创建浏览器缓存方法；
存于sessionStorage和localStorage
##### 插件 plugins
###### 自定义指令 directives
> 全局自定义指令

    Vue.directive('customName', {
        //钩子bind | inserted | update | componentUpdated | unbind
        //指令钩子函数会被传入以下参数：el, binding, vnode, oldVnode;
        !只有el可修改，其他参数都是只读的
        bind: function(el, binding, vnode, oldVnode){
            ToDo()
        }
    })
    
    // 如果插入img后图片加载出错
    <img v-defaultImg={value: defaultImgSrc} :src="imgSrc">
    
    Vue.directive('defaultImg', {
        inserted: function (el, binding) {
            el.onerror = function () {
                if(binding.value){
                    this.src = binding.value;
                    return
                }
                this.src = defaultImg
            }
        }
    });

[vue官网-自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)

###### 过滤器 filters
> 全局过滤器
    
    <div>{{ price | priceMin}}</div>   
    
    Vue.filter('priceMin', function(value){
        return value > 0 ? value : 0
    })
> 也可以在页面内局部注册
    
    export default {
        filters: {
            customName: function(val) {
                TODO()
            }
        }
    }
[vue官网-自定义过滤器](https://cn.vuejs.org/v2/api/#Vue-filter)
###### 混合 mixins
> 全局混合
> src/core/plugins/mixins/index.js

    import mixins_methods  from './mixins/base/methods'
    Vue.mixin(mixins_methods);
[vue官网-全局混入](https://cn.vuejs.org/v2/guide/mixins.html#%E5%85%A8%E5%B1%80%E6%B7%B7%E5%85%A5)
##### 自定义方法 util
>/src/init.js
    
    import util from 'sendinfo-admin-ui/src/core/util'
    Vue.prototype.util = util;
    
    new Vue({
        method: {
            demo(){
                this.uitl.xxx()
            },
        }
    })
    
    
### 路由 router

---
> src/init.js

    import routerFunction from 'sendinfo-admin-ui/src/router';
    let router = routerFunction(store,route);

> router/router.js

    {
        path: '/',
        name: 'home',
        redirect: '/index',
        component: main,
        children: [
            {
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
    }
    
> component值是vue页面，resolve可以按需加载对应的vue页面。
children内的子路由都嵌套在父级的component内的<router-view>位置

> router/index.js

    import {routers} from './router';
    // 路由过滤拦截
    export default (store,routeConfig={})=>{
        // 所有路由都经过beforeEach和afterEach
        router.beforeEach(to, from, next) => {
            // `to` 和 `from` 都是路由对象
        })
    }

[更多路由介绍](/router)

[vue-router官方文档](https://router.vuejs.org/zh)
### 状态管理器 store

[vue-vuex官方文档](https://vuex.vuejs.org/zh/)
### 样式 styles