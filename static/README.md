## 静态文件 **static**  
### /static
任何放置在 *static* 文件夹的静态资源都会被简单的复制，而不经过 webpack。你需要通过绝对路径来引用它们。

注意我们推荐将资源作为你的模块依赖图的一部分导入，这样它们会通过 webpack 的处理并获得如下好处：

* 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求。
* 文件丢失会直接在编译时报错，而不是到了用户端才产生 404 错误。
* 最终生成的文件名包含了内容哈希，因此你不必担心浏览器会缓存它们的老版本。

**static** 目录提供的是一个应急手段，当你通过绝对路径引用它时，留意应用将会部署到哪里。如果你的应用没有部署在域名的根部，那么你需要为你的 URL 配置 [baseUrl](https://cli.vuejs.org/zh/config/#baseurl) 前缀：
在 public/index.html 或其它通过 html-webpack-plugin 用作模板的 HTML 文件中，你需要通过 <%= BASE_URL %> 设置链接前缀：
```html
<link rel="icon" href="<%= BASE_URL %>favicon.ico">
```
在模板中，你首先需要向你的组件传入基础 URL：

```js
data () {
  return {
    baseUrl: process.env.BASE_URL
  }
}
```
然后：
```html
<img :src="`${baseUrl}my-image.png`">
```

#### 何时使用 **static** 文件夹
* 你需要在构建输出中指定一个文件的名字。
* 你有上千个图片，需要动态引用它们的路径。
* 有些库可能和 webpack 不兼容，这时你除了将其用一个独立的 `<script>` 标签引入没有别的选择。

?> 编译后静态文件夹`static`会被复制到输出目录中

### /src/assets
已require模式引入的静态资源会被webpack编译转化后写入打包文件中；
图片默认小于4kb的会被内联引入，以减少请求数；


## 样式 CSS | sass
/src/styles/..

全局样式采用sass语法；  
Sass 是一款强化 CSS 的辅助工具，它在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能

[查看更多sass中文文档](https://www.sass.hk/docs/)

在init.js中全局加入全局样式文件和字体文件，编译后会插入到页面的head中  
/src/init.js:
```js
// 加载自定义样式
import 'sendinfo-admin-ui/src/styles/common.scss';
// 加载第三方图标 阿里iconfont
import 'sendinfo-admin-ui/src/styles/icon/iconfont.css'
```
页面动画效果样式  
src/animate/vue-transition.scss
```scss
.fade-transverse-leave-active,
.fade-transverse-enter-active {
    transition: all .5s;
}
```
如样式中的**tranisitonName-leave-active** { ... }  
tranisitonName是html中transition的name值
```html
<transition  name="fade-transverse">
    <div v-show="$route.meta.openWay=='9'" class="basic-container">
        <el-card>
            <cache-menu :class="{avueview:$route.meta.openWay=='9'}" ></cache-menu>
        </el-card>
    </div>
</transition>
```

## 字体 font
字体图标样式文件
src/icon/..:

```css
@font-face {font-family: "iconfont";
    src: url('iconfont.eot?t=1534225990008'); /* IE9*/
    src: url('iconfont.eot?t=1534225990008#iefix') format('embedded-opentype'), /* IE6-IE8 */
    url('data:application/x-font-woff;charset=utf-8;base64,d09GRgABA...AwA=') format('woff'),
    url('iconfont.ttf?t=1534225990008') format('truetype'), /* chrome, firefox, opera, Safari, Android, iOS 4.2+*/
    url('iconfont.svg?t=1534225990008#iconfont') format('svg'); /* iOS 4.1- */
}

[class^="el-icon-my"], [class*=" el-icon-my"] {
    font-family:"iconfont" !important;
    font-size:16px;
    font-style:normal;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
}

.el-icon-my-user:before { content: "\e6a0"; }
```
所有class中带有`el-icon-my`的节点都会带上`font-family:"iconfont"`字体样式，
直接在需要用到图标的标签添加对应的class
```html
<i class="el-icon-my-user"></i>
```

## js


## 图片
 /static/assets/images
 /src/assets/images

