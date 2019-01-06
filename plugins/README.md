
## 浏览器缓存 localStore
创建浏览器缓存方法；
存于sessionStorage和localStorage

### 本地存储 webstorage

!> sessionStorage在关闭网站或浏览器后会被删除。存放数据大小为一般为5MB,而且它仅在客户端（即浏览器）中保存，不参与和服务器的通信
localStorage数据会一直保存在硬盘中，除非用户在浏览器手动删除。存放数据大小为一般为5MB,而且它仅在客户端（即浏览器）中保存，不参与和服务器的通信。
Firefox3.6以上、Chrome6以上、Safari 5以上、Pera10.50以上、IE8以上版本的浏览器支持sessionStorage与localStorage的使用

#### 作用域不同
不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享sessionStorage的信息。这里需要注意的是，页面及标 签页仅指顶级窗口，如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的。


### 插件 plugins

## 自定义指令 directives
全局自定义指令

```js
Vue.directive('customName', {
    //钩子bind | inserted | update | componentUpdated | unbind
    //指令钩子函数会被传入以下参数：el, binding, vnode, oldVnode;
    //!只有el可修改，其他参数都是只读的
    bind: function(el, binding, vnode, oldVnode){
        ToDo()
    }
})
```

如果插入img后图片加载出错,则把图片src替换成传入的binding.value,如果没有传入则用默认的defaultImg

    在标签中传入加载出错后的替代图片
    <img v-defaultImg={value: defaultImgSrc} :src="imgSrc">
/src/core/plugins/directives/index.js

```js
import defaultImg from 'sendinfo-admin-ui/src/assets/images/defaultImg.jpg';
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
```

[vue官网-自定义指令](https://cn.vuejs.org/v2/guide/custom-directive.html)

### 过滤器 filters
全局过滤器

```
<div>{{ price | priceMin}}</div>
```
```js
Vue.filter('priceMin', function(value){
    return value > 0 ? value : 0
})
```

也可以在页面内局部注册

```js
export default {
    filters: {
        customName: function(val) {
            TODO()
        }
    }
}
```
[vue官网-自定义过滤器](https://cn.vuejs.org/v2/api/#Vue-filter)
### 混合 mixins
> 全局混合
src/core/plugins/mixins/index.js

```js
import mixins_methods  from './mixins/base/methods'
Vue.mixin(mixins_methods);
```


[vue官网-全局混入](https://cn.vuejs.org/v2/guide/mixins.html#%E5%85%A8%E5%B1%80%E6%B7%B7%E5%85%A5)
### 自定义方法 util
/src/init.js:

```js
import util from 'sendinfo-admin-ui/src/core/util'
Vue.prototype.util = util;

new Vue({
    method: {
        demo(){
            this.uitl.xxx()
        },
    }
})
```