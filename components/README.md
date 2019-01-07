## 自定义组件
[自定义组件api(未完成)]()


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
### 页面实例中单独引入组件
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

### 页面中使用组件

如/components/vInput组件

/src/views/sysOptLog.vue:
```html
<el-input @keyup.enter.native="handleFilter" style="width: 180px;" class="filter-item" placeholder="主题"
                      v-model="paginations.searchData.title"></el-input>

<v-input v-model="formData.logType"  type="select" :dictList="$options.dictOptions['logType']" />
```

#### 向组件中传入数据
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

#### 通过事件向父级组件发送消息 $emit
/src/components/vInput/src/vInput.vue:
```js
data(){
    return {
        innerValue: this.value
    }  
},
watch: {
    innerValue(newVal) {
        this.$emit('input', newVal);
    }
},
```
在watch到`innerValue`发生改变时, 通过$emit向外部通知新的input值`newVal`

在父组件 v-input上使用`v-on:input` 监听内部通知的新值, 这里由于input使用了`v-model="innerValue"`双向绑定，等同于`v-on:input="innerValue"`
```html
<v-input v-model="innerValue"></v-input>
```

官方说明例子：
```js
<input v-model="searchText">
```
等价于：
```js
<input
  v-bind:value="searchText"
  v-on:input="searchText = $event.target.value"
>
```
当用在组件上时，`v-model` 则会这样：
```js
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
### 插槽 slot
内部组件 - /src/components/treeTable/treeGrid.vue:   
```html
<template v-if="columns.length===0">
    <el-table-column  width="150">
        <template slot-scope="scope">
            <span v-for="space in scope.row._level"
                  class="ms-tree-space">
            </span>
            <button class="button is-outlined is-primary is-small" v-if="toggleIconShow(0,scope.row)"
                    @click="toggle(scope.$index)">
                <i v-if="!scope.row._expanded" class="el-icon-plus"></i>
                <i v-else class="el-icon-minus"></i>
            </button>
            <!--后期要想办法将此处的值换成自定义字段的首字段-->
            {{scope.$index}}
        </template>
    </el-table-column>
    <slot></slot>
</template>
```

页面中使用组件 - /src/views/admin/sysFunction.vue:   
```html
<tree-grid czWidth="400" ref="treeGrid" :formateData="$options.dictOptions" :columns="columns" :tree-structure="true"
           :data-source="dataList">
    <template slot-scope="scope">
        <el-button type="primary" icon="el-icon-edit" size="mini"  @click="handleUpdate(scope.row)">编辑</el-button>
        <el-button size="mini" icon="el-icon-delete" type="danger" @click="handleDelete([scope.row])">删除</el-button>
        <el-button type="info" icon="el-icon-setting" size="mini" @click="handleOperation(scope.row)">页面控件权限</el-button>
    </template>
</tree-grid>
```
`<template slot-scope="scope"></template>` 内元素插入到组件内部的 命名为scope(`slot-scope="scope"`) 的插槽中

#### 未命名插槽的情况:
```html
<tree-grid>
    该文本会出现在默认插槽(<solt></slot>)中
</tree-grid>
```
!> 组件内部必须要有一个`<slot></slot>`接受外部传入的内容，否则会被抛弃

#### 具名插槽
```html
<tree-grid>
    <template slot="tree"></template>
    <h1 slot="header">header</h1>
</tree-grid>
```
在组件内部：
```html
<template>
    <slot name="header"></slot>
    <slot name="tree"></slot>
    <slot></slot>
</template>
```

#### 作用域插槽
##### slot-scope:   
用于将元素或组件表示为作用域插槽。
特性的值应该是可以出现在函数签名的参数位置的合法的 JavaScript 表达式。这意味着在支持的环境中，你还可以在表达式中使用 ES2015 解构。它在 `2.5.0+` 中替代了 `scope`。   
**此属性不支持动态绑定**。

##### scope
用于表示一个作为带作用域的插槽的` <template>` 元素，它在 `2.5.0+` 中被 `slot-scope` 替代。

[vue-官方文档：插槽](https://cn.vuejs.org/v2/guide/components-slots.html)

### 开发插件
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