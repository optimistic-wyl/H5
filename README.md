#  H5的开发经验
## 移动端自适应方案
### 写页面时的一些规范
1. css、html、js文件编码统一使用UTF-8 
2. 尽量使用HTML5新标签，使代码更加语义化
3. 禁止使用#定义样式，避免与js的id发生冲突，统一使用类
4. 不在style中定义样式，除特殊情况（百分比之类）
5. js脚本统一放到body之后
6. 样式大块之间需要添加注释，且空一行
7. 交互和动画需要添加注释说明
8. 特殊图标使用网页代码，例如： ©使用&copy
9. 样式属性之间、括弧与代码之间不留空格
10. 样式命名需要见名知意，建议不要使用1、2、3、4或者a、b、c、d之类
11. 提炼通用结构，使用扩展类进行不同样式的定义
12. 大背景图片大小控制在60kb以内（100kb）
13. 模块的标题使用h1-h6，根据模块重要程度选择使用
14. 尽量简化标签结构层次
15. html结构合理简单清晰
16. 能用代码实现尽量不用使用图片，用以减少请求数量
17. 列表结构尽量使用ul ol dl
18. 模块结构划分合理，不能讲同一块信息分在不同结构上
19. 切图需要分析哪些元素需要动态获取的（img数据类的），哪些元素是固定不变的（bg修饰类的）以达到结构可扩展性<br>
20. 给不确定的字段留够空间，超出使用
21. 合理使用绝对和相对定位
22. 多尝试使用css3盒布局和其他新属性
23. 考虑多种状态的存在（列表页、无数据提示、loading提示、报错等等）
24. 扩展性要求高的结构宽高不要定死
25. 能加title的地方尽量加上title
26. 纯图片的结构里边需要有文字，使用text-indent隐藏
27. 合并雪碧图，目前我们有使用字体图标，base64、图片

### 关于viewport
先来了解一点关于viewport的知识，通常我们采用如下代码设置viewport:<br>
  `<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">`
这样整个网页在设备内显示时的页面宽度就会等于设备逻辑像素大小，也就是device-width。这个device-width的计算公式为：<br>

设备的物理分辨率/(devicePixelRatio * scale)，在scale为1的情况下，device-width = 设备的物理分辨率/devicePixelRatio 。dpr是devicePixelRatio的简写，也就是屏幕分辩比。<br>

历史原因，由于苹果retina的产生，使得清晰度提升，主要是因为设备像素提升了一倍，因此可以用更多像素去绘画更清晰的图像.<br>
坊间对于dpr更通俗的说法叫<br>
* 一倍屏<br>
* 两倍屏<br>
* 三倍屏<br>
devicePixelRatio称为设备像素比，每款设备的devicePixelRatio都是已知，并且不变的，目前高清屏，普遍都是2，不过还有更高的，比如2.5, 3 等，我魅族note的手机的devicePixelRatio就是3。淘宝触屏版布局的前提就是viewport的scale根据devicePixelRatio动态设置：在devicePixelRatio为2的时候，scale为0.5,在devicePixelRatio为3的时候，scale为0.3333.<br>
<br>
这么做目的当然是为了保证页面的大小与设计稿保持一致了，比如设计稿如果是750的横向分辨率，那么实际页面的device-width，以iphone6来说，也等于750，这样的话设计稿上标注的尺寸只要除以某一个值就能够转换为rem了。通过js设置viewport的方法如下：<br>
<br>
<pre>
    <code>
    var scale = 1 / devicePixelRatio;document.querySelector('meta[name="viewport"]').setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
    </code>
</pre>

这里给出完美视口<br>
`<meta name="viewport" content="initial-scale=1.0,width=device-width,user-scalable=0,maximum-scale=1.0"/>`

在移动端，低端无定制的需求，都可以用这个完美视口完成。<br>

### 把视觉稿中的px转换成rem

在html上增加一个font-size样式。之后页面中的元素，都可以用rem单位来设置。html上的font-size就是rem的基准像素。<br>
首先，目前视觉稿大小分为640，750以及，1125这三种。<br>
当前方案会把这3类视觉稿分成100份来看待（为了以后兼容vh，vw单位）。每一份被称为一个单位a。同时，1rem单位认定为10a。拿750的视觉稿举例：
<pre>
<code>
1a = 7.5px 
1rem = 75px 
</code>
</pre>
因此，对于视觉稿上的元素的尺寸换算，只需要原始px值除以rem基准px值即可。例如240px * 120px的元素，最后转换为3.2rem * 1.6rem。
手动设置rem基准值的方法<br>
输出如下css样式即可：<br>
<pre>
<code>
html {font-size: 60px!important;}
</code>
</pre>
在实际项目中，把与元素尺寸有关的css，如width,height,line-height,margin,padding等都以rem作为单位，这样页面在不同设备下就能保持一致的网页布局。举例来说，网页有一个.item类，设置了width为3.4rem，然后根据不同的页面尺寸用css3媒体查询去更改设置font-size值。<br>
各个屏幕的rem基准值：
<pre><code>
    html{font-size:10px;}
    @media screen and (min-width:321px) and (max-width:375px){html{font-size:11px;}}
    @media screen and (min-width:376px) and (max-width:414px){html{font-size:12px;}}
    @media screen and (min-width:415px) and (max-width:639px){html{font-size:15px;}}
    @media screen and (min-width:640px) and (max-width:719px){html{font-size:20px;}}
    @media screen and (min-width:720px) and (max-width:749px){html{font-size:22.5px;}}
    @media screen and (min-width:750px) and (max-width:799px){html{font-size:23.5px;}}
    @media screen and (min-width:800px){html{font-size:25px;}}
</code></pre>
(1). item类在所有设备下的width都是3.4rem，但在不同分辨率下的实际像素是不一样的，所以在有些分辨率下，width的界面效果不一定合适，有可能太宽，有可能太窄，这时候就要对width进行调整，那么就需要针对.item写媒介查询的代码，为该分辨率重新设计一个rem值。然而，这里有7种媒介查询的情况，css又有很多跟尺寸相关的属性，哪个属性在哪个分辨率范围不合适都是不定的，最后会导致要写很多的媒介查询才能适配所有设备，而且在写的时候rem都得根据某个分辨率html的font-size去算，这个计算可不见得每次都那么容易，比如40px / 23.5px，这个rem值口算不出来吧！由此可见这其中的麻烦有多少。 
<br>
(2). 以上代码中给出的7个范围下的font-size不一定是合适的，这7个范围也不一定合适，实际有可能不需要这么多，所以找出这些个范围，以及每个范围最合适的font-size也很麻烦 
<br>
(3). 设计稿都是以分辨率来标明尺寸的，前端在根据设计稿里各个元素的像素尺寸转换为rem时，该以哪个font-size为准呢？这需要去写才能知道。<br>
[详细关于rem的说明请参考腾讯ISUS] (http://isux.tencent.com/web-app-rem.html)
<br>

### 字体设置
使用无衬线字体
<pre>
<code>
body {
    font-family: "Helvetica Neue", Helvetica, STHeiTi, sans-serif;
}
</code>
</pre>
iOS 4.0+ 使用英文字体 Helvetica Neue，之前的iOS版本降级使用 Helvetica。中文字体设置为华文黑体STHeiTi。<br> 需补充说明，华文黑体并不存在iOS的字体库中(http://support.apple.com/kb/HT5878)， 但系统会自动将华文黑体 STHeiTi<br> 兼容命中系统默认中文字体黑体-简或黑体-繁<br>
Heiti SC Light 黑体-简 细体 （iOS 7后废弃）<br>
Heiti SC Medium 黑体-简 中黑<br>
Heiti TC Light 黑体-繁 细体<br>
Heiti TC Medium 黑体-繁 中黑<br>
原生Android下中文字体与英文字体都选择默认的无衬线字体<br>
4.0 之前版本英文字体原生 Android 使用的是 Droid Sans，中文字体原生 Android 会命中 Droid Sans Fallback<br>
4.0 之后中英文字体都会使用原生 Android 新的 Roboto 字体<br>
其他第三方 Android 系统也一致选择默认的无衬线字体<br>

#### [字体不使用rem的方法](https://github.com/amfe/lib-flexible)
JS动态计算（常见做法）<br>
根据不同屏幕宽度计算不同字号大小。<br>
1. 定基准值,设计稿是750宽度（2倍屏）,字体的大小是24px.
2. 计算指定宽度的字体大小。
var fontSize = width / 750 * 24 ;<br>
根据dpr设定 （比较好的做法）<br>
ps : 一般时初始化时设置为根元素html的attribute，<br>
window.document.documentElement.setAttribute('dpr',window.devicePixelRatio)<br>
然后css这样写<br>
[dpr=1] { font-size=16px;}<br>
[dpr=2] {font-size=32px; }<br>
字体的大小不推荐用rem作为单位。所以对于字体的设置，仍旧使用px作为单位，并配合用data-dpr属性来区分不同dpr下的的大小。<br>
例如：<br>
div {width: 1rem;  height: 0.4rem;font-size: 12px; // 默认写上dpr为1的fontSize}<br>
[data-dpr="2"] div { font-size: 24px;}<br>
[data-dpr="3"] div {font-size: 36px;}<br>
手动配置dpr<br>
引入执行js之前，可以通过输出meta标签方式来手动设置dpr。语法如下：<br>
<meta name="flexible" content="initial-dpr=2,maximum-dpr=3" /><br>
其中initial-dpr会把dpr强制设置为给定的值，maximum-dpr会比较系统的dpr和给定的dpr，取最小值。注意：这两个参数只能选其一。<br>

### 使用Flexible实现H5页面的终端适配

Flex是Flexible Box的缩写，意为"弹性布局"，用来为盒状模型提供最大的灵活性。<br>
伸缩盒模型（flexbox）是一个新的盒子模型，主要优化了UI布局。作为实际布局的第一个CSS模块（浮动真的应该主要用来制作文本围绕图片这样的效果），它使很多任务容易多。Flexbox的功能主要包手：简单使用一个元素居中（包括水平垂直居中），可以让扩大和收缩元素来填充容器的可利用空间，可以改变源码顺序独立布局，以及还有其他的一些功能。<br>
flexbox的版本：<br>
<pre>
<code>
  display: -webkit-box;  /* 老版本语法: Safari,  iOS, Android browser, older WebKit browsers.  */
  display: -moz-box;    /* 老版本语法: Firefox (buggy) */
  display: -ms-flexbox;  /* 混合版本语法: IE 10 */
  display: -webkit-flex;  /* 新版本语法： Chrome 21+ */
  display: flex;       /* 新版本语法： Opera 12.1, Firefox 22+ */
  <br>
  /*垂直居中*/   
  /*老版本语法*/
  -webkit-box-align: center;
  -moz-box-align: center;
  /*混合版本语法*/
  -ms-flex-align: center;
  /*新版本语法*/
  -webkit-align-items: center;
  align-items: center;

  /*水平居中*/
  /*老版本语法*/
  -webkit-box-pack: center;
  -moz-box-pack: center;
  /*混合版本语法*/
  -ms-flex-pack: center;
  /*新版本语法*/
  -webkit-justify-content: center;
  justify-content: center;
<br><br>
/*实现文本垂直居中*/
h1 {
  display: -webkit-box;
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;

  -webkit-box-align: center;
  -moz-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;//最新版本

  height: 10rem;
}
</code></pre>
横向的弹性布局<br>

        <div class="flex-box">
            <div class="flex"></div>
            <div class="flex"></div>
            <div class="flex"></div>
        </div>

横向弹性布局css样式
<pre>
<code>
.flex-box{display:-webkit-box;display:-ms-flexbox;width: 100%;-webkit-box-sizing: border-box;} 
.flex{-webkit-box-flex:1;-ms-flex:1;width: 0;}
.vc{-webkit-box-align: center;}
.hc{-webkit-box-pack: center;}
</code>
</pre>
纵向的弹性布局<br>

        <div class="ui-row-flex">
            <div class="ui-col ui-col-3"></div>
            <div class="ui-col ui-col-2"></div>
        </div>

纵向弹性布局css样式
<pre>
<code>
.ui-row-flex {display: -webkit-box;width: 100%;-webkit-box-sizing: border-box; box-sizing: border-box;}
.ui-row-flex-ver {-webkit-box-orient: vertical;}
.ui-row-flex .ui-col { -webkit-box-flex: 1;}
.ui-row-flex-ver .ui-col {width: 100%;height: 0;}
.ui-row-flex .ui-col-2 {-webkit-box-flex: 2;}
.ui-row-flex .ui-col-3 {-webkit-box-flex: 3;}
</code>
</pre>
[一个完整的Flexbox指南](http://www.w3cplus.com/css3/a-guide-to-flexbox-new.html)<br>
[W3C Flexbox API](http://www.w3.org/html/ig/zh/css-flex-1/)<br>
[CSS3弹性盒模型生成器](http://the-echoplex.net/flexyboxes/)<br>
[CSS3 flexbox弹性布局实例](http://caibaojian.com/flexbox-example.html#t3)<br>
[Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html)<br>
[Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)<br>
#### 腾讯淘宝各大前端团队对flexbox的开发详解<br>
[淘宝前端团队（FED）移动端开发小记 – Flexbox]<br>
(http://taobaofed.org/blog/2015/11/11/flexbox-in-mobile-web/)<br>
[腾讯全端 AlloyTeam 团队 -浅谈flexbox的弹性盒子布局]<br>
(http://www.alloyteam.com/2015/05/xi-shuo-flexbox-dan-xing-he-zi-bu-ju/)<br>

###  基于display:table的CSS布局

    <ul class="table-equal demo">
        <li>手机</li>
        <li>联系人</li>
        <li>信息</li>
        <li>主屏</li>
    </ul>

<pre><code>  
  .table-equal {
  display: table;
  table-layout: fixed;
  width: 100%;
}
.table-equal li {
  display: table-cell;
} 
</code>
</pre>

### [CSS 模块](http://glenmaddern.com/articles/css-modules)

#### 像局部一样无需考虑全局冲突
在 CSS 模块中,请注意所有的类都是相互独立的，这里并不存在一个“ 基类 ”然后其它的类集成并“ 覆盖 ”它的属性这种情况。在 CSS 模块中 每一个类都必须包含这个元素需要的所有样式，每一个文件被编译成独立的文件。这样我们就只需要使用通用简单的类选择器名字就好了。我们不需要担心它会污染全局的变量。下面我就我们编写一个拥有四个状态的按钮来说明这个功能。<br>
如：按钮模块化<br>
按主流设计风格，移动端操作按钮大概可以分出来以上类型的按钮，<br>
根据网页重构常用思想：具有超强复用性和扩展性的组件，可以为项目节省大量重构时间和开发成本。<br>
那么 操作按钮 的重构也遵循这样的一个最基本的原则——高复用性和强扩展型。<br>

那么这些不同类型的按钮都是在一个原生的按钮基础上衍生出来的，那就是btn。<br>
代码如下：<br>

<pre>
<code>

/* =============================
   名称：btn
   使用说明：操作按钮
            1.标签可使用a或者是button；
            2.标签为button时，type可设置为button、submit、reset；
            3.点击态给标签追加class="active"；
            4.按钮默认为直角，圆角按钮可在外围标签追加class="btn-corner"；
            5.按钮默认无阴影，带阴影按钮可在外围标签追加class="btn-shadow"；
            6.按钮默认块级元素，内联按钮可在外围标签追加class="btn-inline"。
   ============================= */
/* btn 基类 */
.btn{display:block;width:100%;position:relative;margin:.2em 0;padding:.86em 1em;line-height:16px;text-align:center;vertical-align:middle;text-overflow:ellipsis;overflow:hidden;white-space:nowrap;-webkit-box-sizing:border-box;box-sizing:border-box;}
.btn .icon{width:20px;height:20px;display:inline-block;background:#666666;vertical-align:middle;margin:-3px 5px 0 5px;border-radius:20px;-webkit-border-radius:20px;}
.btn .icon img{width:100%;height:100%;}
.btn-icon-left .icon{float:left;margin:0;}
.btn-icon-right .icon{float:right;margin:0;}
.btn-notxt .btn{text-indent:-90em;}
/* corner-all 圆角按钮 */
.btn-corner .btn{border-radius:4px;-webkit-border-radius:4px;}
/* btn-shadow 带阴影按钮 */<br>
.btn-shadow .btn{box-shadow:0 3px 3px rgba(0,0,0,0.3);}
/* btn-inline 内联按钮 */
.btn-inline .btn{display:inline-block;width:auto;}
/* mini-btn 微型按钮 */
.btn-mini .btn{padding:.5em 1em;font-size:0.8em;}
/* btn-green 绿色按钮扩展类 */
.btn-green{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #36CC36), color-stop(1, #19A819));background-image:linear-gradient(to bottom, #36CC36, #19A819);background-color:#19A819;color:#FFFFFF;}<br>
.btn-green.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #19A819), color-stop(1, #36CC36));background-image:linear-gradient(to bottom, #19A819, #36CC36);}
/* btn-red 红色按钮扩展类 */
.btn-red{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #C50C0C), color-stop(1, #AE0B0B));background-image:linear-gradient(to bottom, #C50C0C, #AE0B0B);background-color:#BA0C0C;color:#FFFFFF;}<br>
.btn-red.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #AE0B0B), color-stop(1, #C50C0C));background-image:linear-gradient(to bottom, #AE0B0B, #C50C0C);}
/* btn-blue 蓝色按钮扩展类 */
.btn-blue{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #425ebe), color-stop(1, #2f4691));background-image:linear-gradient(to bottom, #425ebe, #2f4691);background-color:#BA0C0C;color:#FFFFFF;}<br>
.btn-blue.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #425ebe), color-stop(1, #425ebe));background-image:linear-gradient(to bottom, #425ebe), color-stop, #425ebe);}
/* btn-red-s 红色小按钮扩展类 */
.btn-red-s, .btn-red-s.active{height:30px;min-width:80px;max-width:100px;line-height:30px;background-color:#E04241;border:1px solid #CC0000;box-shadow:0 0 3px rgba(0,0,0,0.7);text-shadow:0 -1px 0 #621D1D;}
/* btn-gray 灰色按钮扩展类 */
.btn-gray{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #F8F8F8), color-stop(1, #E1E1E1));background-image:linear-gradient(to bottom, #F8F8F8, #E1E1E1);background-color:#EBEBEB;color:#555555;}<br>
.btn-gray.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #E1E1E1), color-stop(1, #F8F8F8));background-image:linear-gradient(to bottom, #E1E1E1, #F8F8F8);}
/* btn-disable 禁用按钮扩展类 */
.btn-disable, .btn-disable.active{background-image:none;background-color:#E1E1E1;color:#919191}<br>
/* btn-white 白色按钮扩展类 */
.btn-white{background-color:#FFFFFF;color:#333333;}
.btn-white.active{background-color:#FFFFFF;color:#333333;}
/* btn-white-s 白色小按钮扩展类 */
.btn-white-s{background-color:#FFFFFF;border:1px solid #CEC9B4;color:#333333;}
.btn-white-s.active{background-color:#FFFFFF;border:1px solid #CEC9B4;color:#333333;}
/* btn-del 删除按钮扩展类 */
.btn-del{width:40px;height:23px;line-height:23px;font-size:12px;background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #FF424F), color-stop(1, #EC1214));background-image:linear-gradient(to bottom, #FF424F, #EC1214);background-color:#AC000C;border:1px solid #AC000C;border-radius:3px;}
.btn-del.active{background-image:-webkit-gradient(linear, left top, left bottom, color-stop(0, #EC1214), color-stop(1, #FF424F));background-image:linear-gradient(to bottom, #EC1214, #FF424F);}
</code>
</pre>
所有按钮除了都是基于btn开发进行扩展之外，还有<br>
点击态：如果使用系统默认点击态，那么btn再添加一个属性即可：-webkit-tap-highlight-color:rgba(0,0,0,0.1);/* 设置点击链接或按钮时遮罩层为10%透明 */；<br>
  ​如果自定义点击态样式，追加active；<br>
不可点击态：追加disabled。<br>
庆幸的是，类似.btn-white.active（.A.B.C）的连接（多类）class书写形式，移动端是支持的非常好的，所以完全可以大胆使用。
按钮形态虽多，但在正式项目中，很少会同时出现N多种形态。<br>
所以操作按钮算是移动端网页中最容易开发的页面元素了。<br>
之所以一一例举出来，只是能够为大家整理出来一些移动端可能会遇到的操作按钮提供参考，当然也很是实用的哦。<br>
按钮开发虽易，维护不易，且行且珍惜。<br>









/*****************************************************************************************************************/
著作权归作者所有。
商业转载请联系作者获得授权,非商业转载请注明出处。
链接:http://caibaojian.com/mobile-responsive-example.html
来源:http://caibaojian.com

互联网上的自适应方案到底有几种呢？就我个人实践所知，有这么几种方案：

固定一个某些宽度，使用一个模式，加上少许的媒体查询方案
使用flexbox解决方案
使用百分比加媒体查询
使用rem
淘宝最近开源的一个框架和网易的框架有同工之异。都是采用rem实现一稿解决所有设置自适应。在没出来这种方案之前，第一种做法的人数也不少。类似以下说到的拉钩网。看一下流云诸葛的文章。

以下摘自：从网易与淘宝的font-size思考前端设计稿与工作流

1. 简单问题简单解决
我觉得有些web app并一定很复杂，比如拉勾网，你看看它的页面在iphone4,iphone6,ipad下的样子就知道了：





它的页面有一个特点，就是：

顶部与底部的bar不管分辨率怎么变，它的高度和位置都不变
中间每条招聘信息不管分辨率怎么变，招聘公司的图标等信息都位于条目的左边，薪资都位于右边
这种app是一种典型的弹性布局：关键元素高宽和位置都不变，只有容器元素在做伸缩变换。对于这类app，记住一个开发原则就好：文字流式，控件弹性，图片等比缩放。以图描述：



这个规则是一套基本的适配规则，对于这种简单app来说已经足够，同时它也是后面要说的rem布局的基础。另外对于拉勾这种app可能需要额外媒介查询对布局进行调整的就是小屏幕设备。举例来说，因为现在很多设计稿是根据iphone6的尺寸来的，而iphon6设备宽的逻辑的像素是375px，而iphone4的逻辑像素是320个像素，所以如果你根据设计稿做出来的东西，在iphone4里面可能显示不下，比如说拉钩网底部那个下载框，你对比看下就知道了，这是4：



这是6：



6下面两边的间距比4多很多，说明拉勾对4肯定是做过适配的，从代码也可以证实这一点：



不过如果你拿到的是根据4的设计稿，那就没有问题，比4分辨率大的设备肯定能显示根据4的尺寸做出来的东西。

还有一点，这种情况css尺寸单位用px就好，不要用rem，避免增加复杂度。

2. 网易的做法
先来看看网易在不同分辨率下，呈现的效果：





从上面几张图可以看出，随着分辨率的增大，页面的效果会发生明显变化，主要体现在各个元素的宽高与间距。375*680的比320*680的导航栏明显要高。能够达到这种效果的根本原因就是因为网易页面里除了font-size之外的其它css尺寸都使用了rem作为单位，比如你看导航栏的高度设置代码：



可是在本文第1部分提到，使用rem布局结合在html上根据不同分辨率设置不同font-size有很多不好解决的麻烦，网易是如何解决的呢？最根本的原因在于，网易页面上html的font-size不是预先通过媒介查询在css里定义好的，而是通过js计算出来的，所以当分辨率发生变化时，html的font-size就会变，不过这得在你调整分辨率后，刷新页面才能看得到效果。你看代码就知道为啥font-size是直接写到html的style上面的了（js设置的原因）：



它是根据什么计算的，这就跟设计稿有关了，拿网易来说，它的设计稿应该是基于iphone4或者iphone5来的，所以它的设计稿竖直放时的横向分辨率为640px，为了计算方便，取一个100px的font-size为参照，那么body元素的宽度就可以设置为width: 6.4rem，于是html的font-size=deviceWidth / 6.4。这个deviceWidth就是viewport设置中的那个deviceWidth。根据这个计算规则，可得出本部分开始的四张截图中html的font-size大小如下：

deviceWidth = 320，font-size = 320 / 6.4 = 50px
deviceWidth = 375，font-size = 375 / 6.4 = 58.59375px
deviceWidth = 414，font-size = 414 / 6.4 = 64.6875px
deviceWidth = 500，font-size = 500 / 6.4 = 78.125px
事实上网易就是这么干的，你看它的代码就知道，body元素的宽是：



根据这个可以肯定它的设计稿竖着时的横向分辨率为640。然后你再看看网易在分辨率为320*680，375*680，414*680，500*680时，html的font-size是不是与上面计算的一致：



320*680



375*680



414*680



500*680

这个deviceWidth通过document.documentElement.clientWidth就能取到了，所以当页面的dom ready后，做的第一件事情就是：

document.documentElement.style.fontSize = document.documentElement.clientWidth / 6.4 + 'px';
这个6.4怎么来的，当然是根据设计稿的横向分辨率/100得来的。下面总结下网易的这种做法：

（1）先拿设计稿竖着的横向分辨率除以100得到body元素的宽度：
如果设计稿基于iphone6，横向分辨率为750，body的width为750 / 100 = 7.5rem
如果设计稿基于iphone4/5，横向分辨率为640，body的width为640 / 100 = 6.4rem
（2）布局时，设计图标注的尺寸除以100得到css中的尺寸，比如下图：

播放器高度为210px，写样式的时候css应该这么写：height: 2.1rem。之所以取一个100作为参照，就是为了这里计算rem的方便！
（3）在dom ready以后，通过以下代码设置html的font-size:
document.documentElement.style.fontSize = document.documentElement.clientWidth / 6.4 + 'px';
6.4只是举个例子，如果是750的设计稿，应该除以7.5。
（4）font-size可能需要额外的媒介查询，并且font-size不能使用rem，如网易的设置：
@media screen and (max-width:321px){
    .m-navlist{font-size:15px}
}

@media screen and (min-width:321px) and (max-width:400px){
    .m-navlist{font-size:16px}
}

@media screen and (min-width:400px){
    .m-navlist{font-size:18px}
}
最后还有2个情况要说明：

第一，如果采用网易这种做法，视口要如下设置：

<meta name="viewport" content="initial-scale=1,maximum-scale=1, minimum-scale=1">
第二，当deviceWidth大于设计稿的横向分辨率时，html的font-size始终等于横向分辨率/body元素宽：



640*680



641*680

之所以这么干，是因为当deviceWidth大于640时，则物理分辨率大于1280（这就看设备的devicePixelRatio这个值了），应该去访问pc网站了。事实就是这样，你从手机访问网易，看到的是触屏版的页面，如果从pad访问，看到的就是电脑版的页面。如果你也想这么干，只要把总结中第三步的代码稍微改一下就行了：

var deviceWidth = document.documentElement.clientWidth;
if(deviceWidth > 640) deviceWidth = 640;
document.documentElement.style.fontSize = deviceWidth / 6.4 + 'px';
3. 淘宝的做法
看看淘宝在不同分辨率下，呈现的效果：



淘宝的效果跟网易的效果其实是类似的，随着分辨率的变化，页面元素的尺寸和间距都相应变化，这是因为淘宝的尺寸也是使用了rem的原因。在介绍它的做法之前，先来了解一点关于viewport的知识，通常我们采用如下代码设置viewport:

<meta name="viewport"   content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
这样整个网页在设备内显示时的页面宽度就会等于设备逻辑像素大小，也就是device-width。这个device-width的计算公式为：

设备的物理分辨率/(devicePixelRatio * scale)，在scale为1的情况下，device-width = 设备的物理分辨率/devicePixelRatio 。

devicePixelRatio称为设备像素比，每款设备的devicePixelRatio都是已知，并且不变的，目前高清屏，普遍都是2，不过还有更高的，比如2.5, 3 等，我魅族note的手机的devicePixelRatio就是3。淘宝触屏版布局的前提就是viewport的scale根据devicePixelRatio动态设置：



在devicePixelRatio为2的时候，scale为0.5



在devicePixelRatio为3的时候，scale为0.3333

这么做目的当然是为了保证页面的大小与设计稿保持一致了，比如设计稿如果是750的横向分辨率，那么实际页面的device-width，以iphone6来说，也等于750，这样的话设计稿上标注的尺寸只要除以某一个值就能够转换为rem了。通过js设置viewport的方法如下：

var scale = 1 / devicePixelRatio;
document.querySelector('meta[name="viewport"]').setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
淘宝布局的第二个要点，就是html元素的font-size的计算公式，font-size = deviceWidth / 10：



接下来要解决的问题是，元素的尺寸该如何计算，比如说设计稿上某一个元素的宽为150px，换算成rem应该怎么算呢？这个值等于设计稿标注尺寸/该设计稿对应的html的font-size。拿淘宝来说的，他们用的设计稿是750的，所以html的font-size就是75，如果某个元素时150px的宽，换算成rem就是150 / 75 = 2rem。总结下淘宝的这些做法：

（1）动态设置viewport的scale
var scale = 1 / devicePixelRatio;
document.querySelector('meta[name="viewport"]').setAttribute('content','initial-scale=' + scale + ', maximum-scale=' + scale + ', minimum-scale=' + scale + ', user-scalable=no');
（2）动态计算html的font-size
document.documentElement.style.fontSize = document.documentElement.clientWidth / 10 + 'px';
（3）布局的时候，各元素的css尺寸=设计稿标注尺寸/设计稿横向分辨率/10
（4）font-size可能需要额外的媒介查询，并且font-size不使用rem，这一点跟网易是一样的。
最后还有一个情况要说明，跟网易一样，淘宝也设置了一个临界点，当设备竖着时横向物理分辨率大于1080时，html的font-size就不会变化了，原因也是一样的，分辨率已经可以去访问电脑版页面了。





关于这种做法的具体实现，淘宝已经给我们提供了一个开源的解决方案，具体请查看：

https://github.com/amfe/lib-flexible

之前没有找到这相关的资料，实在不好意思：（

4. 比较网易与淘宝的做法
共同点：

都能适配所有的手机设备，对于pad，网易与淘宝都会跳转到pc页面，不再使用触屏版的页面
都需要动态设置html的font-size
布局时各元素的尺寸值都是根据设计稿标注的尺寸计算出来，由于html的font-size是动态调整的，所以能够做到不同分辨率下页面布局呈现等比变化
容器元素的font-size都不用rem，需要额外地对font-size做媒介查询
都能应用于尺寸不同的设计稿，只要按以上总结的方法去用就可以了
不同点

淘宝的设计稿是基于750的横向分辨率，网易的设计稿是基于640的横向分辨率，还要强调的是，虽然设计稿不同，但是最终的结果是一致的，设计稿的尺寸一个公司设计人员的工作标准，每个公司不一样而已
淘宝还需要动态设置viewport的scale，网易不用
最重要的区别就是：网易的做法，rem值很好计算，淘宝的做法肯定得用计算器才能用好了 。不过要是你使用了less和sass这样的css处理器，就好办多了，以淘宝跟less举例，我们可以这样编写less：
//定义一个变量和一个mixin
@baseFontSize: 75;//基于视觉稿横屏尺寸/100得出的基准font-size
.px2rem(@name, @px){
    @{name}: @px / @baseFontSize * 1rem;
}
//使用示例：
.container {
    .px2rem(height, 240);
}
//less翻译结果：
.container {
    height: 3.2rem;
}
5. 如何与设计协作
前端与设计师的协作应该是比较简单的，最重要的是要规范设计提供给你的产物，通常对于前端来说，我们需要设计师提供标注尺寸后的设计稿以及各种元素的切图文件，有了这些就可以开始布局了。考虑到Retina显示屏以及这么多移动设备分辨率却不一样的问题，那么设计师应该提供多套设计稿吗？从网易和淘宝的做法来看，应该是不用了，我们可以按照设计稿，先做出一套布局，按照以上方法做适配，由于是等比适配，所以各个设备的视觉效果差异应该会很小，当然也排除不了一些需要媒介查询特殊处理的情况，这肯定避免不了的。下面这张图是淘宝设计师分享的他们的工作流程：



解释一下就是：

第一步，视觉设计阶段，设计师按宽度750px（iphone 6）做设计稿，除图片外所有设计元素用矢量路径来做。设计定稿后在750px的设计稿上做标注，输出标注图。同时等比放大1.5倍生成宽度1125px的设计稿，在1125px的稿子里切图。

第二步，输出两个交付物给开发工程师：一个是程序用到的@3x切图资源，另一个是宽度750px的设计标注图。

第三步，开发工程师拿到750px标注图和@3x切图资源，完成iPhone 6（375pt）的界面开发。此阶段不能用固定宽度的方式开发界面，得用自动布局（auto layout），方便后续适配到其它尺寸。

第四步，适配调试阶段，基于iPhone 6的界面效果，分别向上向下调试iPhone 6 plus（414pt）和iPhone 5S及以下（320pt）的界面效果。由此完成大中小三屏适配。

注意第三步，就要使用我们以上介绍的网易跟淘宝的适配方法了。假如公司设计稿不是基于750的怎么办，其实很简单，按上图做一些相应替换即可，但是流程和方法还是一样的。解释一下为什么要在@3x的图里切，这是因为现在市面上也有不少像魅蓝note这种超高清屏幕，devicePixelRatio已经达到3了，这个切图保证在所有设备都清晰显示。

6. 总结
总算是罗里吧嗦地把文章写完了， 希望你还觉得满意，这篇文章对我来说价值也很大，今后做html5的项目就有思路了，本文提到的三种方法将来肯定都有用武之地。最后，欢迎大家在评论里与我交流你对本文的看法，我们可以一起交流，一起进步。
