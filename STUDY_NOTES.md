# study notes
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
