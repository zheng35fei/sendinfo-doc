
* template 页面模板
* script 页面局部vue实例
* 页面样式 `<style lang="scss" scoped>`  scoped 表示只在当前内部vue实例中生效

### 样式
`<style lang="scss" scoped>` 
lang 表示是 哪一种类型的css 语法； 项目内使用sass, 也可以使用less、 css 、stylus等
scoped 表示当前样式只在当前文档生效
局部样式
```html
<header data-v-8265a8dc="" class="el-header avue-tabs" style="height: auto;"></header>
```
标签上会自动生成data-v-xxx，
局部样式也会生成对应前缀
```css
[data-v-xxx].customCssName {
    font-size:12px;
}
```

#### 局部样式覆盖全局样式、框架样式
在样式class前添加 `>>>` 符号, 并且用 `!important` 提升样式权重：
```css
>>> .avue-tabs {
    color:red !important;
}
```

?> 也可以不加scoped `<style></style>`  变成全局样式；但是由于各个模块的加载顺序问题，会导致污染别的不需要该样式的模块，或者被别的模块覆盖。

### html
```html
<template>
    <div class="login" :style="{backgroundImage: 'url(' +loginBgImg + ')'}" @keydown.enter="handleSubmit">
        <div class="login-con">
            <el-card class="box-card">
                <p slot="header">
                    <i class="el-icon-circle-check"></i>
                    欢迎登录
                </p>
                <div class="form-con">
                    <el-form ref="loginForm" :model="form" :rules="rules">
                        <el-form-item prop="username">
                            <el-input v-model.trim="form.username" placeholder="请输入用户名">
                            <span slot="prepend">
                                    <i class="el-icon-my-user"></i>
                                </span>
                            </el-input>
                        </el-form-item>
                        <el-form-item prop="password">
                            <el-input type="password" v-model.trim="form.password" placeholder="请输入密码">
                            <span slot="prepend">
                                    <i :size="14" class="el-icon-my-wodemima"></i>
                                </span>
                            </el-input>
                        </el-form-item>
                        <el-form-item prop="verifyCode">
                            <el-row :span="24">
                                <el-col :span="14">
                                    <el-input size="small"
                                              :maxlength="6"
                                              v-model="form.verifyCode"
                                              auto-complete="off"
                                              placeholder="请输入验证码">
                                        <i slot="prefix"
                                           class="icon-yanzhengma"></i>
                                    </el-input>
                                </el-col>
                                <el-col :span="10">
                                    <div class="login-code">
                                        <img :src="verifyCodeUrl"
                                             class="login-code-img"
                                             @click="refreshCode"/>
                                    </div>
                                </el-col>
                            </el-row>
                        </el-form-item>

                        <el-form-item>
                            <el-button size="medium" type="primary" @click="handleSubmit" style="width: 100%">立即登录
                            </el-button>
                        </el-form-item>
                    </el-form>
                </div>
            </el-card>
        </div>
    </div>
</template>
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

### 生命周期
![生命周期](./../imgs/lifecycle.png)

#### created
进入页面通常在created中查询页面显示数据的接口

#### beforeDestory
离开当前页面之前触发， 如当前页面存在定时器需要在这里清除

### 计算属性

```js
new Vue({
    data(){
        return {
            isShowMask: false,
            isAdult: true,
            sex: 'man',
            userInfo: {
                name: 'username',
                age: 12
            },
            list: [{id:1, price:10}, {id:2, price:101}]
        }
    },
    computed:{
        // 简化提取对象中的某一个属性：
        // 普通数据渲染用户年龄
        // <span>{{userInfo.age}}</span>
        // 使用计算属性显示年龄
        // <span>{{userAge}}</span>
        userAge(){
            return this.userInfo.age
        },
        // 结合其他属性一起判断自定义属性的布尔值
        isRealName(){
            return this.isAdult && this.sex === 'man'
        },
        // 筛选列表数据
        newList(){
            return this.list.map(item => item.price > 100)
        }
    }
})
```

```html
普通数据渲染用户年龄
<span>{{userInfo.age}}</span>

使用计算属性显示年龄
<span>{{userAge}}</span>
```

?> 我们可以将同一函数定义为一个方法而不是一个计算属性。两种方式的最终结果确实是完全相同的。然而，不同的是**计算属性是基于它们的依赖进行缓存的**。只在相关依赖发生改变时它们才会重新求值。这就意味着只要 message 还没有发生改变，多次访问 reversedMessage 计算属性会立即返回之前的计算结果，而不必再次执行函数。 

这也同样意味着下面的计算属性将不再更新，因为 `Date.now()` 不是响应式依赖：
```js
computed: {
  now: function () {
    return Date.now()
  }
}
```

#### get、set
计算属性默认只有 getter ，不过在需要时你也可以提供一个 setter ：
set: isShowBlur赋值为false时， 关闭所有弹层;   
get: 多个弹层数据有一个为true（展示弹出层）时，`isShowBlur`值设置为true;
```html
<div v-if="isShowBlur" class="mask-ticket" @click="isShowBlur = false"></div>
```
```js
export default {
    data(){
        return {
            calendar: {
                show:false
            },
            isShowTicket: false,
            limitLayer: false
        }  
    },
    computed: {
        isShowBlur: {
            get: function () {
                return this.isShowTicket || this.calendar.show || this.limitLayer
            },
            set: function(v){
                this.isShowTicket = v;
                this.calendar.show = v;
                this.limitLayer = v;
            }
        },
    }
}

```

官方文档例子
```js
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```
现在再运行 `vm.fullName = 'John Doe'` 时，*setter* 会被调用，`vm.firstName` 和 `vm.lastName` 也会相应地被更新。




### 绑定数据和方法
?> `v-on` 和 `v-bind`  
缩写 `@` 、 `:`  
```js
data() {
    return {
        imgUrl: '/imgs/logo.png'
    }
},
methods: {
    toLogin(){
        // TODO
    }
}
```

```html
<div @click="toLogin()">button</div>
<div v-on.click="toLogin()">button</div>

<img :src="imgUrl" alt="">
<img v-bind.src="imgUrl" alt="">
```

### 数据遍历
```js
data() {
    return {
        List: [
            {
                name: 'aaa'
            },
            {
                name: 'bbb'
            }
        ]
    }
},
```
```html
<ul>
    <li v-for="item in List">{{item.name}}</li>
</ul>
```

### 条件渲染
```html
<div v-if="isShowDiv"></div>
<div v-else></div>
```

### 展示隐藏
```html
<!--<div stlye="display:none"></div>-->
<div v-show="isShowDiv"></div>

<!--div不渲染-->
<div v-if="isShowDiv"></div>

```

### 双向绑定
```js
data(){
    return {
        inputValue: 1
    }
}
```
input的值是定义的inputValue,同时输入值也会改变inputValue
```html
<input type="text" v-model="inputValue">
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