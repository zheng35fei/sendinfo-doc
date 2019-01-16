# 页面
* template 页面html模板
* script 页面局部vue实例
* 页面样式 `<style lang="scss" scoped>`  scoped 表示只在当前内部vue实例中生效

## 样式
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

### 局部样式覆盖全局样式、框架样式
在样式class前添加 `>>>` 符号, 并且用 `!important` 提升样式权重：
```css
>>> .avue-tabs {
    color:red !important;
}
```

?> 也可以不加scoped `<style></style>`  变成全局样式；但是由于各个模块的加载顺序问题，会导致污染别的不需要该样式的模块，或者被别的模块覆盖。

## 页面数据绑定
html中嵌套数据使用 `{{}}` 符号包裹, 如果需要不转化显示代码或图片等内容可在标签内使用`v-html`、`v-text`渲染；
```html
let price = 10.01
let content = '<div><img src="aaa.jpg"></div>'

<span>{{price}}</span>

<span>￥{{price => 0 ? price : 0}}</span>

<div class="contentWrap" v-html="content"></div>
```


登录页面：
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

!> `template`下一级只能包含一个dom元素，否则会报错; 这个规则不包括组件的使用。
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

### 绑定数据和方法
`v-on(@)` 和 `v-bind(:)`  ()内缩写
```js
data(){
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

一些简单方法也可以直接内联写在html标签内   
```html
<div @click="console.log(1)"></div>
```

#### 事件修饰符
在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 `v-on` 提供了事件修饰符。之前提过，修饰符是由点开头的指令后缀来表示的。
* `stop`
* `prevent`
* `capture`
* `self`
* `once`
* `passive`

```html
<!-- 阻止单击事件继续传播 -->
<a v-on:click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form v-on:submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a v-on:click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form v-on:submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即元素自身触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div v-on:click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div v-on:click.self="doThat">...</div>
```

#### 按键修饰符、系统修饰键、鼠标按钮修饰符
在监听键盘事件时，我们经常需要检查常见的键值。Vue 允许为 `v-on` 在监听键盘事件时添加按键修饰符：
```js
<!-- 只有在 `keyCode` 是 13 时调用 `vm.submit()` -->
<input v-on:keyup.13="submit">
```
[查看按键修饰符具体文档](https://cn.vuejs.org/v2/guide/events.html#%E6%8C%89%E9%94%AE%E4%BF%AE%E9%A5%B0%E7%AC%A6)

[查看系统修饰键具体文档](https://cn.vuejs.org/v2/guide/events.html#%E7%B3%BB%E7%BB%9F%E4%BF%AE%E9%A5%B0%E9%94%AE)

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
    <li v-for="item in List" :key="item.id">{{item.name}}</li>
</ul>
```
!> 遍历数据需要在遍历的标签上添加一个不重复的`key`属性，来确保每一条的唯一性

### 动态添加class
根据数据动态添加class
`:class={className: Boolean}`, `:class`可以和`class`同时使用
````html
let isTotalPrice = true;

<div :class="{total: isTotalPrice}" class="price"></div>

<!--result-->
<div class="price total"></div>
````

### 条件渲染
`v-if v-else` 不同于 `v-show`, `if else`页面上不会生成对应标签
```html
<div v-if="isShowDiv"></div>
<div v-else></div>
```
也可以在`template`上使用
```html
<template v-if="isShow"></template>
<template v-else></template>
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

### 获取页面元素
```html
<div ref="table"></div>
<!--也可以给组件添加ref, 获取到的是组件实例对象-->
<v-calender ref="calendar"></calender>

```
```js
// 获取元素DOM后可以通过js方法操作他
this.$refs.table
```

## 页面vue实例

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

常用的生命周期钩子
### 生命周期
![生命周期](./../imgs/lifecycle.png)

#### created
进入页面通常在created中查询页面显示数据的接口

#### mounted   
`el` 被新创建的 `vm.$el` 替换，并挂载到实例上去之后调用该钩子。如果 root 实例挂载了一个文档内元素，当 `mounted` 被调用时 `vm.$el` 也在文档内。

注意 `mounted` 不会承诺所有的子组件也都一起被挂载。如果你希望等到整个视图都渲染完毕，可以用 [vm.$nextTick](https://cn.vuejs.org/v2/api/#vm-nextTick) 替换掉 `mounted`：

#### beforeDestory
离开当前页面之前触发， 如当前页面存在定时器需要在这里清除


### 属性 data
可以在页面实例化时添加初始定义；    
如页面遮罩`isShowMask`默认是`false`,默认隐藏状态；   
或者`listData`:列表数据， 默认是空对象或数组，当接口返回数据后赋值给`listData`; `this.listData = res.data`

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
})
```
当data中的数据发生变化时，视图会重新渲染； 如果计算属性中有依赖`data`中的值时，也会重新触发方法，生成新的结果；
只有在实例被创建时，`data`中的属性才是响应式的。

!>  如果要往data中的响应式属性添加默认新的属性时，需要使用`$set`方法插入。否则插入的属性不会是响应式的，不会触发视图的更新和计算属性的更新。
```ue.set( target, key, value )```
```js
new vue({
    data() {
        return {
            a: {
                b: 1
            }
        }
    }
})
this.$set(this.a, 'c', 2)
// result
a: {
    b: 1,
    c: 2
}
```

### 计算属性 computed

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


### 侦听器 watch
虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法，来响应数据的变化。当需要在数据变化时执行异步或开销较大的操作时，这个方式是最有用的。

```js
new vue({
    data() {
        return {
            listData: []
        }
    },
    watch: {
        'listData': function(newListData, oldListData) {
            console.log(newListData)
        }
    }
})
```

### 自定义方法 methods
methods 将被混入到 Vue 实例中。可以直接通过 `VM` 实例访问这些方法，或者在指令表达式中使用。方法中的 `this` 自动绑定为 Vue 实例。
```js
new Vue({
    // 在实例创建完成后，立即调用获取列表数据方法
    created() {
        this.getListData()  
    },
    methods: {
        // 获取列表数据
        getListData(){
            const url = '/admin/role/list';
            let params = {
                page:1,
                limit:10
            };
            this.$http.get(url, params).then( res => {
                this.listData = res.rows;
            })
        },
        // 可以显示遮罩层, 在html上绑定click方法
        showMask() {
            this.isShowMask = true;
        }
    }
})
```