
## 浏览器缓存 localStore
/src/core/localStore/index.js
创建浏览器缓存方法；
存于sessionStorage和localStorage;  
sessionStorage和localStorage的get、set、remove方法都是一样的

### 本地存储 webstorage

!> sessionStorage在关闭网站或浏览器后会被删除。存放数据大小为一般为5MB,而且它仅在客户端（即浏览器）中保存，不参与和服务器的通信
localStorage数据会一直保存在硬盘中，除非用户在浏览器手动删除。存放数据大小为一般为5MB,而且它仅在客户端（即浏览器）中保存，不参与和服务器的通信。
Firefox3.6以上、Chrome6以上、Safari 5以上、Pera10.50以上、IE8以上版本的浏览器支持sessionStorage与localStorage的使用

#### 作用域不同
不同浏览器无法共享localStorage或sessionStorage中的信息。相同浏览器的不同页面间可以共享相同的 localStorage（页面属于相同域名和端口），但是不同页面或标签页间无法共享sessionStorage的信息。这里需要注意的是，页面及标 签页仅指顶级窗口，如果一个标签页包含多个iframe标签且他们属于同源页面，那么他们之间是可以共享sessionStorage的。


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

## 过滤器 filters
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
            // TODO
        }
    }
}
```
[vue官网-自定义过滤器](https://cn.vuejs.org/v2/api/#Vue-filter)

## 混合 mixins
> 全局混合

全局加载/mixins/base下的所有方法  
src/core/plugins/mixins/index.js:
```js
import mixins_methods  from './mixins/base/methods'
import mixins_dictMap  from './mixins/base/dictMap'
import mixins_dictMapEnum  from './mixins/base/dictMapEnum'
import mixins_paramMap  from './mixins/base/paramMap'
import mixins_paramMapNew  from './mixins/base/paramMapNew'

Vue.mixin(mixins_methods);
Vue.mixin(mixins_dictMap);
Vue.mixin(mixins_dictMapEnum);
Vue.mixin(mixins_paramMap);
Vue.mixin(mixins_paramMapNew);
```
例如 /base/methods.js：
```js
export default {
    methods: {
        $getMediaUrl(path){
            if(!path){
                return '';
            }
            const check = /^(https?|ftp|file):\/\/.+$/;
            if(check.test(path)){
                return path
            }else{
                return this.setting.FILE_MEDIA_URL + path
            }
        },
    }
}
```

页面实例中使用$getMediaUrl方法：
/src/views/components/upload-file/src/mediaUpload.vue:
```js
computed:{
    imgSrc(){
        return this.$getMediaUrl(this.currentValue);
    },
},
```
根据传入的地址，拼接上init.js中定义好的mediaUrl。  

### 页面中加载使用
如获取验证码图片地址： /src/core/plugins/mixins/verify-code.js   
混入了methods内的方法到当前vue实例的methods中，可以像在当前methods中的方法一样调用
```js
export default {
    methods: {
        getVerifyCodeUrl (businessCode,type){
            if(!businessCode){
                throw '业务编码不能为空'
            }
            if(!this.$store.getters.sessionId){
                this.$store.commit('INIT_SESSIONID');
            }
            return `${this.setting.VERIFY_CODE_URL}/${businessCode}/?SESSIONID=${this.$store.getters.sessionId}&time=${new Date().getTime()}`
        }
    }
}
```

/src/views/login.vue:
```js
import verifyCode from 'sendinfo-admin-ui/src/core/plugins/mixins/verify-code.js'
export default {
    mixins: [verifyCode],
    methods: {
        refreshCode () {
            this.verifyCodeUrl = this.getVerifyCodeUrl('login')
        },
    }
}
```

## $options
在实例中定义了option，可以在当前实例中取值、赋值   
上面的mixin的dictMap.js就是在初始化字典方法中，在回调中给this.$options.dictOptions赋值了接口返回的数据
```js
new Vue({
  customOption: 'foo',
  created: function () {
    console.log(this.$options.customOption) // => 'foo'
  }
})
```
也可以在methods方法内部调用别的方法
```js
new Vue({
    el: '#app',
    data(){
        return {
            
        }
    },
    methods: {
        a(){
            this.b()
        },
        b() {
            this.$options.methods.a()
        }
    }
})
```

[vue官网-全局混入](https://cn.vuejs.org/v2/guide/mixins.html#%E5%85%A8%E5%B1%80%E6%B7%B7%E5%85%A5)
## 自定义方法 util
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