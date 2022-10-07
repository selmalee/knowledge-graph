# 3 html&css

## 3.1 水平居中居中和垂直居中

- 行内元素：
  1. 父元素`text-align:center;` `line-height:父元素高度;`
  2. 父元素`display:table-cell;`

- 块级元素：

  1. 父元素为相对定位，子元素绝对定位

     ``` css
     top: 50%;
     left:50%;
     margin-top: -元素高度的一半px;
     margin-left: -元素宽度的一半px;
   // 或者 transform: translateY(-50%); display: block;
     ```

  2. 父元素为相对定位，子元素绝对定位
  
     ``` css
   top: 0; right: 0; bottom: 0; left: 0; margin: auto;
     ```

  3. 父元素
  
     ``` css
     display: flex;
     align-items: center;// 垂直居中
     justify-content: center;// 水平居中
     ```



## 3.2 CSS3

### 3.2.1 动画

- transform 2D转换

  - translate()
  - rotate()
  - scale()
  - skew()
  - matrix()

  ``` css
  div
  {
  	transform: rotate(30deg);
  	-ms-transform: rotate(30deg); /* IE 9 */
  	-webkit-transform: rotate(30deg); /* Safari and Chrome */
  }
  ```

- transition 过渡

  ``` css
  div
  {
      transition: width 1s linear 2s;
      /* Safari */
      -webkit-transition:width 1s linear 2s;
  }
  ```

- @keyframes 规则是创建动画

  ``` css
  @keyframes myfirst
  {
      from {background: red;}
      to {background: yellow;}
  }
  ```

- animation动画

  ``` css
  div
  {
      animation: myfirst 5s linear 2s infinite alternate;
      /* Safari 与 Chrome: */
      -webkit-animation: myfirst 5s linear 2s infinite alternate;
  }
  ```

### 3.2.2 动画GPU加速

GPU是图形处理单位。浏览器多进程架构中，GPU进程独立于其他进程处理 GPU 任务。它被分成不同的进程，因为 GPU 处理来自多个应用程序的请求并将它们绘制在同一个表面上。

在CSS3不那么普及的IE8时代中，我们做页面动画基本都是利用js定时器改变元素位置来做类似平移，幻灯等效果。用改变属性值的办法我们难免会触发代价高昂的repaint/reflow。

对于transform/opacity 这两种变换，浏览器不会用repaint/reflow处理，而是在已经渲染好的元素基础上进行附加工作。例如一个黑底色的div,往右飞100px, 传统JS过程是对每次修改left值后重新画一个div。而如果我们用`transform:translate(0,100px);transition:2s;` 浏览器则是把这个绘制好的div单独放在一个画面层再平移这个层过去，div的几何形状，颜色不会再重复计算，而是保留在这个图层中。

![](https://pic1.zhimg.com/80/v2-12da78f7d66326774f01e99e37791cac_1440w.jpg)

> TIPS: chrome devtools 中可以开启 Rendering 中的 Layer borders 查看图层纹理。
> 其中黄色边框表示该元素有 3d 变换，表示放到一个新的复合层（composited layer）中渲染，蓝色栅格表示正常的 render layer。

render tree -> 渲染元素 -> 图层 -> GPU 渲染 -> 浏览器复合图层 -> 生成最终的屏幕图像

在 GPU 渲染的过程中，一些元素会因为符合了某些规则，而被提升为独立的层（黄色边框部分），一旦独立出来，就不会影响其它 DOM 的布局，所以我们可以利用这些规则，将经常变换的 DOM 主动提升到独立的层，那么在浏览器的一帧运行中，就可以避免回流和重绘了。

浏览器什么时候会创建一个独立的复合图层呢？事实上一般是在以下几种情况下：

- 3D 或者 CSS transform
-  `<video>` 和 `<canvas>` 标签
- CSS filters
- 元素覆盖时，比如使用了 `z-index` 属性
- 对开发者来说，当某一部分需要用独立的层渲染，我们可以使用 css 属性`will-change`让浏览器创建层。
- 为了避免 2D transform 动画在开始和结束时发生的 repaint 操作，我们可以硬编码一些样式来解决这个问题：

```
.example1 {
  transform: translateZ(0);
}

.example2 {
  transform: rotateZ(360deg);
}

复制代码
```

这段代码的作用就是让浏览器执行 3D transform。浏览器通过该样式创建了一个独立图层，图层中的动画则有GPU进行预处理并且触发了硬件加速。



[使用css3实现动画来开启GPU加速](https://juejin.cn/post/6844904047560884232)

[Chrome浏览器架构（译文）](https://xie.infoq.cn/article/5d36d123bfd1c56688e125ad3)

[Inside look at modern web browser (part 1)](https://developers.google.com/web/updates/2018/09/inside-browser-part1#site-isolation)

[重绘，回流和合成，了解基本浏览器绘制帮你优化页面性能](https://zhuanlan.zhihu.com/p/23428399)

### 3.2.3 flex布局

![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071004.png)

设置在容器flex container （display: flex;）上的属性：

- flex-direction: row | row-reverse | column | column-reverse;

  ![img](http://www.ruanyifeng.com/blogimg/asset/2015/bg2015071005.png)

- flex-wrap: nowrap | wrap | wrap-reverse;

- justify-content: flex-start | flex-end | center | space-between | space-around; 

  定义了项目在主轴上的对齐方式。

- align-items: flex-start | flex-end | center | baseline | stretch;

  定义项目在交叉轴上如何对齐。

- align-content

设置在项目item上的属性：

- `flex-grow`定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。

- `flex-shrink` 定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。

- `flex-basis`属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。

- **`flex`** [flex](https://developer.mozilla.org/zh-CN/docs/Web/CSS/flex)规定了弹性元素如何伸长或缩短以适应flex容器中的可用空间。这是一个__简写属性__，用来设置 `flex-grow`, `flex-shrink` 与 `flex-basis`

  ``` css
  /* One value, unitless number: flex-grow */
  flex: 2;
  /* One value, width/height: flex-basis */
  flex: 10em;
  flex: 30px;
  /* Two values: flex-grow | flex-basis */
  flex: 1 30px;
  /* Two values: flex-grow | flex-shrink */
  flex: 2 2;
  /* Three values: flex-grow | flex-shrink | flex-basis */
  flex: 2 2 10%;
  ```

### 3.2.4 渐变背景等

## 3.3 html5

- canvas

  ``` html
  <canvas id="myCanvas" width="200" height="100"></canvas>
  ```

  ``` js
  var c=document.getElementById("myCanvas");
  var ctx=c.getContext("2d");
  ctx.fillStyle="#FF0000";
  ctx.fillRect(0,0,150,75);
  /*
  ctx.beginPath();
  ctx.arc(95,50,40,0,2*Math.PI);
  ctx.stroke()
  */
  ```

- 新元素

  header, section, footer, aside, nav, main, article, figure,媒体audio, video

- 本地存储`localStorage`，`sessionStorage`、本地SQL数据`openDatabase`（日志上报缓存）



## 3.3 设备兼容性&浏览器兼容性

### 3.3.1 设备兼容性

先了解下各种像素：

- 屏幕物理像素：单位是pt，比如**分辨率**：1136pt x 640pt
- css像素：单位是px或者dp
- 屏幕像素密度：简称ppi，单位是dpi，指屏幕水平或垂直每英寸有多少个物理像素。ppi越高，图像会更加清晰。
- dpr：dpr = 物理像素 / CSS像素   即每个物理像素有多少个css像素

各种视口：

- 布局视口：桌面浏览器中，浏览器窗口就是约束你的CSS布局视口；在手机上，布局视口与移动端浏览器屏幕宽度不相关联。

  ![img](https://camo.githubusercontent.com/22d25ea74b8de7878488bd2ce1163ced9ccb5a26/68747470733a2f2f692e696d6775722e636f6d2f55724e7a6e64442e6a7067)

  设置布局视口宽度

  ```css
  <meta name="viewport" content="width=640">
  ```

- 媒体查询与布局视口：

  700px 指的是布局视口的宽度

  ``` css
  @media (min-width: 700px){
      // ...
  }
  ```

- 视觉视口：用户正在看到的网页的区域

- 用户缩放：缩放的是设备像素，会影响视觉视口，但不会影响css像素，不会影响布局视口。比如一个宽度为 200px 的元素无论放大，还是200个CSS像素。但是因为这些像素被放大了，所以CSS像素也就跨越了更多的设备像素。

理想视口设置：

把布局视口的宽度改成屏幕的宽度，设置缩放`initial-scale=1`与`width=device-width`的效果是一样的

``` css
<meta name="viewport" content="width=device-width,initial-scale=1">
```



__方案：__

1. 如上理想视口设置

2. ` flexible`方案

   `rem` 是相对于`html`节点的`font-size`来做计算的，因此通过设置`document.documentElement.style.fontSize`就可以统一整个页面的布局标准。

3. `vh`、`vw`方案（浏览器兼容性较差）

   - `vw(Viewport's width)`：`1vw`等于视觉视口的`1%`
   - `vh(Viewport's height)` ：`1vh` 为视觉视口高度的`1%`
   
4. media queries

   ``` css
   @media screen and (max-width: 600px) { /*当屏幕尺寸小于600px时，应用下面的CSS样式*/
     /*你的css代码*/
   }
   ```

   ``` css
   @media (orientation: landscape) {} // 横屏
   @media (resolution: 150dpi) {
     p {
       color: red;
     }
   } // dpi
   ```

   ``` css
   @media only screen and (-webkit-min-device-pixel-ratio:2){
     .avatar{
       background-image: url(conardLi_2x.png); // dpr2
     }
   }
   ```

   关于图片还可以使用svg、设置`srcset`等方法

   ``` html
   <img src="conardLi_1x.png"
        srcset=" conardLi_2x.png 2x, conardLi_3x.png 3x">
   ```

   

5. flex布局：高度定死，宽度自适应，元素都采用`px`做单位。

   随着屏幕宽度变化，页面也会跟着变化，效果就和PC页面的流体布局差不多。

   浏览器支持比`vw`好。

### 3.3.3 浏览器兼容性

浏览器内核：

浏览器内核可以分成两部分：**渲染引擎**(layout engineer 或者 Rendering Engine)和 **JS 引擎**。

- 渲染引擎负责取得网页的内容（HTML、XML、图像等等）、整理讯息（例如加入 CSS 等），以及计算网页的显示方式，然后会输出至显示器或打印机。
- JS 引擎则是解析 Javascript 语言，后来 **JS 引擎越来越独立，内核就倾向于只指渲染引擎**。

主流浏览器：

1、IE浏览器内核：Trident内核，也被称为IE内核；

2、Chrome浏览器内核：Chromium内核 → Webkit内核 → Blink内核；

3、Firefox浏览器内核：Gecko内核，也被称Firefox内核；

4、Safari浏览器内核：Webkit内核；

css中兼容

``` css
.flex-v {
    display: box;              /* OLD - Android 4.4- */
    display: -webkit-box;      /* OLD - iOS 6-, Safari 3.1-6 */
    display: -moz-box;         /* OLD - Firefox 19- (buggy but mostly works) */
    display: -ms-flexbox;      /* TWEENER - IE 10 */
    display: -webkit-flex;     /* NEW - Chrome */
    display: flex;             /* NEW, Spec - Opera 12.1, Firefox 20+ */
}
```

webpack中先用预处理器把less、sass转为css，然后再通过`Autoprefixer`给编译好的css加前缀

## 3.4 BFC（Block formatting context 块格式化上下文）

**块级格式化上下文**：一个独立的渲染区域，只有Block-level box（块级盒）参与，它规定了内部的Block-level Box（块级盒）如何布局，并且与这个区域外部毫不相干。

只要元素满足下面任一条件即可触发 BFC 特性：

- body 根元素
- 浮动元素：float 除 none 以外的值
- 绝对定位元素：position (absolute、fixed)
- display 为 inline-block、table-cells、flex
- overflow 除了 visible 以外的值 (hidden、auto、scroll)

BFC的特点：

1. 内部的Box会在垂直方向，一个接一个地放置；
2. Box自身垂直方向的位置由margin-top决定，属于同一个BFC的两个相邻Box的margin会发生重叠；
3. BFC 可以包含浮动的元素，并可以阻止元素被浮动元素覆盖

引申问题：

1. 外边距坍塌：如果想要避免外边距的重叠，可以将其放在不同的 BFC 容器（设置`overflow: hidden`）中。
2. **清除浮动**：设置父容器`overflow: hidden`，父容器就可以与内部浮动元素等高
3. 两栏布局，阻止元素被浮动元素覆盖：设置元素`overflow: hidden`



## 3.5 一些实践总结

### 3.5.1 无限滚动

https://fed.taobao.org/blog/2019/09/02/make-infinite-scroll/

列表组件只关心和渲染限定范围（视窗可见区域+前后预Buffer数量）内的元素。

每当滚动触发导致元素移出视窗，组件会计算并填充渲染范围内变化的部分，并回收多余的DOM节点。

如果渲染范围内的元素还未取到所需的数据，则会先渲染占位元素，同时将缺失的数据条数通知给调用方，在异步数据返回后渲染并替换占位元素。

按需加载数据，能根据滚动的实际情况返回缺少数据的列表项数量，调用方可以弹性的设置pageSize

### 3.5.2 PWA

https://ymedialabs.com/progressive-web-apps

### 3.5.3瀑布流布局

multi-columns

flex

js 设置top left