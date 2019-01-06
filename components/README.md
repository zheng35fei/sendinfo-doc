## 组件

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

### 页面中使用组件

如/components/vInput:

/src/views/sysOptLog.vue:
```html
<el-input @keyup.enter.native="handleFilter" style="width: 180px;" class="filter-item" placeholder="主题"
                      v-model="paginations.searchData.title"></el-input>

<v-input v-model="formData.logType"  type="select" :dictList="$options.dictOptions['logType']" />
```

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


## elementUI 和 AVUE 框架组件
框架组件在Init.js中加载框架时已加入了，可以在页面中直接使用。如`el-table`等标签可直接生成对应的页面元素。

[element](http://element-cn.eleme.io/#/zh-CN/component/installation)


[avue](https://avue.top/#/component/installation)