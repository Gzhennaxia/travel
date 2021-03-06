# study notes
## Vue

### Keep Alive

Keep Alive 是 Vue 中自带的标签，用来缓存页面，当使用了 Keep Alive 标签后，页面就多了一个生命周期函数 activated，每次重新回到页面时都会触发该钩子函数。结合临时缓冲变量可以实现更新缓存的作用。



当对整个应用进行页面缓存时，可以指定某些页面不缓存，使用 `<keep-alive>` 标签的 exclude 属性指定排除的组件。

```html
<keep-alive exclude="Detail">
  <router-view/>
</keep-alive>
```

### 递归组件

组件中 name 属性一般在递归组件里会用到，在组件中调用自身时标签名就是 name 属性的值。

### 组件的 name 属性

组件中 name 属性的作用：

1. 递归组件中使用 name 属性值作为递归标签
2. vue 调试工具中显示的组件名对应于 name 属性
3. keep alive 中剔除组件时使用 name 属性指定要剔除的组件

## 移动端开发注意事项

### 样式重置
```html
<!--  移动端需要设置 minimum-scale=1.0,maximum-scale=1.0,er-scalable=no  -->
<!--  作用是让页面窗口固定为 1：1 不可伸缩  -->
<meta name="viewport" content="width=device-width,initial-scale=1.0,minimum-scale=1.0,maximum-scale=1.0,er-scalable=no">
```
### 1 像素问题
> [移动端1px像素解决方式,从1px像素问题剖析像素及viewport](http://www.fly63.com/article/detial/145)
项目中使用 body.css 文件解决

### 一倍图与二倍图
> 比如你需要一个30pt*30pt的图标，那么这时你就需要跟美工（美术工程师）要三个文件：
>
> 一个是30px*30px的PNG图片，用原名来命名即可，例如sample.png。
>
> 一个是60px*60px的PNG图片，这个要用原名@2x来命名，例如sample@2x.png。
>
> 一个是90px*90px的PNG图片，这个要用原名@3x来命名，例如sample@3x.png。
>
> 1x的图标用于1:1的屏幕，非视网膜屏，例如iPhone 3GS、（/*20140414DEL*/ //早期iPhone 4）非视网膜屏的众多iPad等。
>
> 2x的图标用于1:2的屏幕，大部分视网膜屏，机型如（/*20140414ADD*/ iPhone 4、）iPhone 4S、iPhone 5、iPhone 5s、iPhone 6、iPad with Retina及后续的视网膜屏iPad等。
>
> 3x的图标目前应用于1:3的屏幕，即iPhone 6Plus

#### 项目中 px 与 rem 的转化
项目中的 reset.css 文件中定义了 html 的 font-size = 50px

UI 给的是二倍图，所以能得到如下关系：

> 1rem = html font-size = 50px (see in rest.css)
>
> 86px (@2x) = 43px (@1x) = 1 / 50 rem * 43 = 0.86rem

所以可以直接根据 UI 图的尺寸，除以 100 即为 rem 的值。

## CSS
### height 与 inline-height 的区别
> [line-height和height的区别](https://blog.csdn.net/codingalarm/article/details/51916571)

技巧：行高等于元素高，可将文本内容垂直居中

### 防止页面抖动

当用户网速过慢时，如果图片还没加载完，图片下面的元素会先渲染，图片加载完后会再次渲染，导致页面出现抖动。通过以下 css 来防止页面抖动：

```html
<div class="wrapper">
  <swiper :options="swiperOption">
    <swiper-slide v-for="item of imgList" :key="item.id">
      <img class="swiper-img" :src="item.value"/>
    </swiper-slide>
    <div class="swiper-pagination" slot="pagination"></div>
  </swiper>
</div>
```

```css
.wrapper
    overflow: hidden
    width: 100%
    height: 0
    padding-bottom: 26.6% /* 图片宽高比 */
    background: #eee
    .swiper-img
      width: 100%
```

### 样式穿透

为了不影响其他组件的样式，一般在组件的 style 标签中都会加 scope 属性，代表局部样式，只对当前组件起作用。但要修改当前组件的子组件时，需要使用 `>>>` 进行样式穿透。

```html
<div class="wrapper">
    <swiper :options="swiperOption">
      <swiper-slide v-for="item of swiperList" :key="item.id">
        <img class="swiper-img" :src="item.imgUrl"/>
      </swiper-slide>
      <div class="swiper-pagination"  slot="pagination"></div>
    </swiper>
  </div>
```

```css
.wrapper >>> .swiper-pagination-bullet-active
    background: #fff
```

### z-index

[z-index - CSS（层叠样式表） | MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/z-index)

### 背景渐变色

```css
background-image: linear-gradient(top, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.8))
```



## Stylus
### 变量
新建后缀名为 `.styl` 的文件，在 styl 文件中定义变量。例如：`$bgColor = #00bcd4`

在需要的地方引入 styl 文件，注意在 css 中引入样式文件需要在 import 前加上 `@`

```html
<style lang="stylus" scoped>
  @import "../../../assets/styles/variables.styl"
  .header
    background: $bgColor
</style>
```

## webpack
### 路径别名
一些常用的路径名可以在 webpack 中定义别名，方便引用

在项目路径 `build/webpack.base.conf.js` 下：
```js
resolve: {
  extensions: ['.js', '.vue', '.json'],
  alias: {
    'vue$': 'vue/dist/vue.esm.js',
    '@': resolve('src'),
    'styles': resolve('src/assets/styles')
  }
}
```

> 注意：在 css 中使用路径别名需要在前面加上 `~` 
>
> [Webpack 中 css import 使用 alias 相对路径](https://wiki.zthxxx.me/wiki/%E6%8A%80%E6%9C%AF%E5%BC%80%E5%8F%91/%E5%89%8D%E7%AB%AF/Webpack-%E4%B8%AD-css-import-%E4%BD%BF%E7%94%A8-alias-%E7%9B%B8%E5%AF%B9%E8%B7%AF%E5%BE%84/)

## 三方组件
### vue-awesome-swiper
> [GitHub 地址](https://github.com/surmon-china/vue-awesome-swiper)
>
> [Swiper 中文网](https://www.swiper.com.cn/)
当 swiper 元素的父级元素的 DOM 发生变化时，swiper 的宽度计算会发生问题，需要开启 observeParents 和 observe 来促使 swiper 重新计算宽度。

### better-scroll
> [GitHub 地址](https://github.com/ustbhuangyi/better-scroll)
better-scroll 是对 [iscroll](https://github.com/cubiq/iscroll) 对封装

## 请求转发
webpack-dev-server

通过在 `config/index.js` 的 `proxyTable` 中添加如下配置

```js
proxyTable: {
  '/api': {
    target: 'http://localhost:8080',
    pathRewrite: {
      '^/api': '/static/mock'
    }
  }
}
```

Vue 中当元素的 ref 属性是通过 v-for 循环的方式指定的时候，通过 this.$refs[refName] 取到的值是一个数组
```vue
<div
  class="area"
  v-for="(item, key) of cities"
  :key="key"
  :ref="key"
>
  <div class="title border-topbottom">{{key}}</div>
  <div class="item-list">
    <div
      class="item border-bottom"
      v-for="innerItem of item"
      :key="innerItem.id"
    >{{innerItem.name}}</div>
  </div>
</div>
```

## HTML5

### localStorage

Html5 提供了 localStorage 接口用于实现类似 Cookie 的功能。

但当用户关闭了本地存储的功能或者开启了隐身模式时浏览器会报异常，所以一般用到 localStorage 时会将其放到 try catch 里

## JavaScript

### Element.scrollTop

> [Element.scrollTop - Web API 接口参考| MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/scrollTop)

**Element.scrollTop** 属性可以获取或设置一个元素的内容垂直滚动的像素数。

一个元素的 `scrollTop` 值是这个元素的顶部到视口可见内容（的顶部）的距离的度量。

### Document.documentElement

> [document.documentElement - Web API 接口参考| MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Document/documentElement)

**Document.documentElement** 是一个会返回文档对象（document）的根元素的只读属性（如HTML文档的 `<html>` 元素）。

### EventTarget.addEventListener()

> [EventTarget.addEventListener() - Web API 接口参考| MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/EventTarget/addEventListener)

EventTarget.addEventListener() 方法将指定的监听器注册到 EventTarget 上，当该对象触发指定的事件时，指定的回调函数就会被执行。 事件目标可以是一个文档上的元素 Element,Document和Window或者任何其他支持事件的对象 (比如 XMLHttpRequest)。

### Polyfill

[@babel/polyfill · Babel 中文网](https://www.babeljs.cn/docs/babel-polyfill)



## Vuex

### mapState / mapMutations / mapActions

```js
import { mapState, mapMutations } from 'vuex'

computed: {
  ...mapState['city']
},
methods: {
  handleCityClick (city) {
    // this.$store.commit('changeCity', city)
    this.changeCity(city)
    this.$router.push('/')
  },
    ...mapMutations(['changeCity'])
},
```

### getter

Vuex 中的 Getter 类似于计算属性的功能，减少冗余

## Vue-Router

### 滚动行为

> [滚动行为]([https://router.vuejs.org/zh/guide/advanced/scroll-behavior.html#%E5%BC%82%E6%AD%A5%E6%BB%9A%E5%8A%A8](https://router.vuejs.org/zh/guide/advanced/scroll-behavior.html#异步滚动))

当页面切换后上一个页面的滚动会影响当前页面的滚动。解决这个问题只需要在创建 Router 时加上如下配置即可，意思是每次跳转到新页面时让 x / y 轴都从 0 开始。

```js
scrollBehavior (to, from, savedPosition) {
  return { x: 0, y: 0 }
}
```

## WebPack

### 使用IP访问前端项目

webpack server 默认不支持使用 IP 进行访问，通过修改 `package.json` 文件中的 scripts.dev，加上 `--host 0.0.0.0` 即可。实际上我们每次执行的 `nom run dev` 执行的就是 scripts.dev 的指令。

```json
{
  "scripts": {
    "dev": "webpack-dev-server --host 0.0.0.0 --inline --progress --config build/webpack.dev.conf.js"
  }
}
```

### Build

打包命令：`npm run build`

打包后会生成 dist 目录，如果要打包后放在后端项目的 project 目录下，则需要修改 `config/index.js` 文件的 build 配置中的 `assetsPublicPath: '/project'`，然后再重新打包即可。

