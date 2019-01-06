## 页面分布
每个页面包含下面这些：

* template 页面模板
* script 页面局部vue实例
* 页面样式

* ` <style lang="scss" scoped>`  scoped 表示只在内部vue实例中生效


```html
<template></template>
```

### 页面vue实例

```html
<template>
    <div>
        <div class="filter-container">
            <el-input @keyup.enter.native="handleFilter" style="width: 180px;" class="filter-item" placeholder="主题"
                      v-model="paginations.searchData.title"></el-input>
        </div>
    </div>
</template>

<script >
    export default {
        data() {
            return {
                dataList: [],
                deleteRows: [],
                formData: Object.assign({}, tempformData), // copy obj
                labels: tempLabels,
                rules: {
                    info: [
                        {max: 512, message: '最大长度为 64', trigger: 'blur'},
                        {required: true, message: '不为空', trigger: 'blur'},
                    ],
                },
                //需要给分页组件传的信息
                paginations: {
                    currentPage: 1,
                    total: 0,
                    pageSize: 10,
                    pageSizes: [10, 20, 30, 50],
                    layout: 'total, sizes, prev, pager, next, jumper',
                    searchData: {
                        title: '',
                        content: '',
                        logLevel: '',
                        startTime: DateUtil.prevDate(new Date(),3),
                        endTime: '',
                    },
                },
            }
        }
        components: {}, // 引入组件
        created(){},    // 生命周期
        watch: {},      // watch数据变化
        methods: {      // 自定义方法
            handleFilter() {
                this.paginations.currentPage = 1;
                this.getList()
            },
             getList() {
                this.listLoading = true;
                const data = {
                    current: this.paginations.currentPage,
                    size: this.paginations.pageSize,
                    ...this.paginations.searchData,
                    ...this.orders
                };
                this.$http.get('/api/sysOptLog/page', {params: data}).then(response => {
                    this.dataList = response.data.records;
                    this.paginations.total = response.data.total;
                    this.listLoading = false;
                });
            },
        }
    }
</script>
```

!> `template`下一级只能包含一个dom元素，否则会报错
```js
// 错误
<template>
    <div></div>
    <div></div>
</template>

// 正确
<template>
    <div>
        <div></div>
        <div></div>
    </div>
</template>
```

### 暂 访问根实例
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

### 暂 访问父实例
```js
// 获取根组件的数据
this.$parent.foo
```

### 暂 依赖注入
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