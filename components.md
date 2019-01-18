## 组件
页面中可复用的一些元素，如表单、分页、输入框等；可根据传入的不同配置和数据生成想要的页面元素。

## 自定义组件
页面中使用组件：
```html
<component-a :attrA="attrAData"></component-a>
```
组件内部：
```js
export default {
    props:{
        value: [Number, String, Date], // 多个类型
        attrA: Object, // 限制传入类型
        attrB: {
            type: String, // 传入类型
            default: 'abcde' // 默认值
        },
        attrC: {
            type: Object,
            // 对象或数组默认值必须从一个工厂函数获取
            default: function() {
                return {
                    a: 1,
                    b: 2
                }
            }
        },
        // 自定义验证函数
        attrD: {
            validator: function (value) {
                // 这个值必须匹配下列字符串中的一个
                return ['success', 'warning', 'danger'].indexOf(value) !== -1
              }
        }
    }
}
```

type可以是下列中的一个：   
* `String`
* `Number`
* `Boolean`
* `Array`
* `Object`
* `Date`
* `Function`
* `Symbol`


### 全局加载自定义组件
/src/components/index.js:
```js
const components = [
    areas,
    ElSelectTree,
    ztree,
    remoteSelect,
    jqgrid,
    TreeGrid,
    VueUEditor,
    Sticky,
    UploadFile,
    UploadRealFile,
    remoteRadio,
    vInput,
]

const install = function (Vue) {
    components.map(component => {
        Vue.component(component.name, component);
    });
};
export default {
    install
}
```
## 页面实例中单独引入组件
如果不是全局注册过的组件，页面使用前，需先引入组件`import`，然后在vue中注册它`components:{vInput}`。
?> 页面中使用组件，组件名称如果是驼峰命名要改成 '-' 分隔
```html
<v-input :src="value" type="text"></v-input>
```
?> src、type等传入的如果是变量或数字需要用v-bind或简写`:`， 如果是字符串则不需要

```js
import vInput from '/src/components/vInput'

export default {
    data(){
        return {
            value: 1
        }  
    },
    components:{
        vInput
    }
}
```

## 页面中使用组件
如/components/vInput组件

/src/views/sysOptLog.vue:
```html
<el-input @keyup.enter.native="handleFilter" style="width: 180px;" class="filter-item" placeholder="主题"
                      v-model="paginations.searchData.title"></el-input>

<v-input v-model="formData.logType"  type="select" :dictList="$options.dictOptions['logType']" />
```

### 向组件中传入数据
通过 v-bind 组件自定义属性传入， 组件内部通过props 过滤并接收
在组件内部文件 `/src/components/vInput` 中定义了传入的各种值： **props**
```js
props: {
    value: [String, Number, Date],
    type: {
        type: String,
        default: () => {
            return 'text';//text/textarea/select/datetime/date
        }
    },
    placeholder: String,
    isEdit:  {
        type: Boolean,
        default: () => {
            return null;
        }
    },
    disabled: Boolean,
    clearable: {
        type: Boolean,
        default: () => {
            return true;
        }
    },
    isShow: {
        type: Boolean,
        default: () => {
            return true;
        }
    },
    //----select-----
    dictList: Array,//list<Object>
    keySetting:{
        type: Object,
        default() {
            return {
                label:'label',
                value:'value'
            }
        }
    },
    /*canNull: {//是否可以选择空
            type: Boolean,
            default: () => {
                return true;
            }
        },*/
    dictType: String,//字典名
    //----select-----end
},
```

在这里限制传入值的类型。
并且根据不同的`type` 展示不同类型的input 是 *text* 、 *textarea* 或者 *select* 等。

### 通过事件向父级组件发送消息 $emit
组件内方法可以通过`this.$emit`向父级发送内部的值或消息，父级页面的组件标签内绑定相同的name，并给这个name一个方法来接收来自组件内部的消息。    
组件：
```html
<div @click="select()"></div>

<script >
new Vue({
    data() {
        return {
            price: 100
        }
    },
    methods: {
        // this.$emit('组件外部接收的绑定属性', 向外传递的值)
        select() {
            this.$emit('currentPrice', this.price)  
        },
    }
})
</script>
```
父级页面：
```html
<tree-grid @currentPrice="getCurPrice" ref="treeGridComponent"></tree-grid>

<script >
    new Vue({
        methods: {
            getCurPrice(val) {
                console.log(val)
            },
            getCurPrice() {
                // 也可以通过refs直接获取到子组件，并且有他下面的所有属性和方法
                console.log(this.$refs.treeGridComponent.price)
            }
        }
    })
</script>
```
[通过ref获取页面元素](/pages/?id=获取页面元素)

官方说明例子：
```html
<input v-model="searchText">
```
等价于：
```html
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```
当用在组件上时，`v-model` 则会这样：
```html
<custom-input
  v-bind:value="searchText"
  v-on:input="searchText = $event"
></custom-input>
```
组件内部：
```js
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

[vue-官方文档：在组件上使用 `v-model`](https://cn.vuejs.org/v2/guide/components.html#%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8-v-model)
## 插槽 slot
假如需要在组件中插入额外的页面元素，插槽slot就是组件留出空间给使用者插入新元素的口子；
组件中可以插入多个插槽；

### 匿名插槽 <slot>
默认的`slot`不带name的匿名插槽接收外部传入的slot属性的内容：
```html
<default-table>
    <!--组件内插入不带slot属性的内容，会被插入到匿名插槽中`<slot></slot>`-->
    <button>自定义按钮</button>
</default-table>
```
组件内部：
```html
<template>
    <slot></slot>
</template>
```

### 具名插槽 slot=slotName
如果插入到组件内元素存在`slot`属性，并且组件内部同样存在`slot`的name值和他相同，那么插入的元素会被放到该`slot`的位置；不存在相同的slot的name时则会被抛弃掉插入的元素。
```html
<default-table>
    <!--组件内插入不带slot属性的内容，会被插入到匿名插槽中`<slot></slot>`-->
    <button slot="menuLeft">自定义按钮</button>
</default-table>
```
组件内部：
```html
<template>
    <slot name="menuLeft"></slot>
    <slot></slot>
</template>
```

### 带作用域的插槽 slot-scope
如果你需要往表格组件的行内插入一个自定义的按钮，点击这一按钮进行一些操作，但你需要知道这一行的id时，`slot-scope`会带给你需要的这一行的数据
在组件内部的`slot`标签上绑定需要传递给插槽的数据，就可以在外部插槽标签上获取到自定义的数据。
```html
<default-table>
    <!--slot-scop的值可以自定义-->
    <template slot="menuSlot" slot-scope="scope">
        <el-button @click="console.log(scope)"></el-button>
    </template>
    <!--scope输出-->
    {
        data: {
            name: 'abc',
            id: 1
        },
        index: 0
    }
</default-table>
```
组件内部
```html
<script >
new Vue({
    data() {
        return {
            listData: [{
                name: 'abc',
                id: 1
            },{
                name: 'def',
                id: 2
            }]
        }
    }
})
</script>

<template v-for="(item, index) in listData">
    <slot name="menuSlot" :data="item" :index="index"></slot>
</template>
```

#### slot-scope:   
用于将元素或组件表示为作用域插槽。
特性的值应该是可以出现在函数签名的参数位置的合法的 JavaScript 表达式。这意味着在支持的环境中，你还可以在表达式中使用 ES2015 解构。它在 `2.5.0+` 中替代了 `scope`。   
**此属性不支持动态绑定**。

#### scope
用于表示一个作为带作用域的插槽的` <template>` 元素，它在 `2.5.0+` 中被 `slot-scope` 替代。

[vue-官方文档：插槽](https://cn.vuejs.org/v2/guide/components-slots.html)

## 开发插件
Vue.js 的插件应该有一个公开方法 install。这个方法的第一个参数是 Vue 构造器，第二个参数是一个可选的选项对象：

```js
MyPlugin.install = function (Vue, options) {
  // 1. 添加全局方法或属性
  Vue.myGlobalMethod = function () {
    // 逻辑...
  }

  // 2. 添加全局资源
  Vue.directive('my-directive', {
    bind (el, binding, vnode, oldVnode) {
      // 逻辑...
    }
    ...
  })

  // 3. 注入组件
  Vue.mixin({
    created: function () {
      // 逻辑...
    }
    ...
  })

  // 4. 添加实例方法
  Vue.prototype.$myMethod = function (methodOptions) {
    // 逻辑...
  }
}
```

[install说明](https://cn.vuejs.org/v2/guide/plugins.html#%E5%BC%80%E5%8F%91%E6%8F%92%E4%BB%B6)


## elementUI 和 AVUE 框架组件
框架组件在Init.js中加载框架时已加入了，可以在页面中直接使用。如`el-table`等标签可直接生成对应的页面元素。

[element](http://element-cn.eleme.io/#/zh-CN/component/installation)


[avue](https://avue.top/#/component/installation)


## 暂 访问根实例
```js
// 获取根组件的数据
this.$root.foo

// 写入根组件的数据
this.$root.foo = 2

// 访问根组件的计算属性
this.$root.bar

// 调用根组件的方法
this.$root.baz()
```

## 暂 访问父实例
```js
// 获取根组件的数据
this.$parent.foo
```

## 暂 依赖注入
```html
<google-map>
  <google-map-region v-bind:shape="cityBoundaries">
    <google-map-markers v-bind:places="iceCreamShops"></google-map-markers>
  </google-map-region>
</google-map>
```

[依赖注入](https://cn.vuejs.org/v2/guide/components-edge-cases.html#%E4%BE%9D%E8%B5%96%E6%B3%A8%E5%85%A5
)  
在这个组件里，所有 <google-map> 的后代都需要访问一个 `getMap` 方法，以便知道要跟那个地图进行交互。不幸的是，使用 `$parent` 属性无法很好的扩展到更深层级的嵌套组件上。这也是依赖注入的用武之地，它用到了两个新的实例选项：`provide` 和 `inject`

vue父内部实例中
```js
provide: function () {
  return {
    getMap: this.getMap
  }
}
```

vue父组件内部任意子级的实例中
```js
inject: ['getMap']
```

在任意子级的组件中都可以获取到暴露出来的方法。类似于`props`

实际上，你可以把依赖注入看作一部分“大范围有效的 prop”，除了：

* 祖先组件不需要知道哪些后代组件使用它提供的属性
* 后代组件不需要知道被注入的属性来自哪里

!> 然而，依赖注入还是有负面影响的。它将你的应用以目前的组件组织方式耦合了起来，使重构变得更加困难。同时所提供的属性是非响应式的。这是出于设计的考虑，因为使用它们来创建一个中心化规模化的数据跟使用 `$root` 做这件事都是不够好的。如果你想要共享的这个属性是你的应用特有的，而不是通用化的，或者如果你想在祖先组件中更新所提供的数据，那么这意味着你可能需要换用一个像 `Vuex` 这样真正的状态管理方案了。