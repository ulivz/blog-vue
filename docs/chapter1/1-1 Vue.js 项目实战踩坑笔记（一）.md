# Vue.js 项目实战踩坑笔记（一）

# 1 前言

作为一名尤老师的忠实粉丝，尽管在工作中使用的是 [AngularJS][1], 但还是熬了很多夜抽空好好学了学 [Vue][2]，最近也开始跟着慕课网的老师做了一个基于 Vue 的仿饿了么的app项目，收获很多，同时也踩了很多坑，为了日后少踩坑，特作此笔录。

# 2 关于Vue的核心

在全文的开头，我不得不去强调一下**vue**的核心思想，也就是**数据驱动**和**组件化**，数据驱动可以让你更多地把精力放在数据操作上，而无需你在修改数据模型时，还要手动更新对应的视图，这得益于 Vue 的观察者模式。

而组件化更是**Vue**明显优于**ng1**的一点，写久了**ng1**，你会发现，尽管你通过指令减少了很多**html**的书写，但是在**ng1**的各种黑魔法的重重包围下，还要采用远古时期**JQuery**写模板的形式来写**HTML**，这，似乎并不是那么优雅。而**Vue**则 很好地解决了这个问题，一个Vue文件搞定一个组件，请看下面的代码示例, 我们可以发现，Html、CSS、JS都封装在到了一个文件中，是不是非常nice！

```
<template>
  <div class="title">{{ title }}</div>
</template>

<script type="text/ecmascript-6">
  export default {
    data () {
      return {
        title: 'Hello World'
      };
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  .title
    color #f00
    font-weight bold
</style>
```
此外，组件化带来的好处就是整个页面都可以拆分为n个子组件，从而构成一个组建树，如下图：

![此处输入图片的描述][3]

同时，结合 vue-router 可以快速地构建单页应用。（比Angular UI Router比起来还是爽很多，以后我会将两者进行对比....）

好了，关于Vue的核心就聊到这里。开始踩坑了...

---

# 3 eslint踩坑经历

在vue项目目录的 `src/main.js` 中加入分号后：

![image_1b5uab3k6g227i41an5mp6u62a.png-20.8kB][4]

编译报错：

![image_1b5u9is1868f2iq5ch1r9715lgm.png-59.7kB][5]

报错的原因是默认情况下，ESlint是不允许有分号的；但是我们仍然希望代码风格中有分号，得怎么办呢？

在根目录的 `eslintrc.js` 中修改 eslint 的配置：

![image_1b5ua640q7ti135lgncs0usqc13.png-2.6kB][6]

增加圈中的选项如下：

![image_1b5ua9r9eqt21cgocvb2371l4a1t.png-35.2kB][7]

更多的配置可以去看 [eslint - semi][8]

值得注意的是，如此一来，所有采用es6编写的代码在末尾都必须加上分号，否则会报这种错：

![image_1b5uacspsvs4f0f2s71rk71ccq2n.png-22.7kB][9]

还有一种情况，若是我们缩进不对，还会报这种错：

![image_1b5uakob31321m8d69ooe7t3i3h.png-27.1kB][10]

配置的在线地址：[eslint - indent][11]

打开上述的在线地址，我们会发现，这是配置我们的缩进是基于tab还是基于空格，但是我们希望能够采用webstorm 的默认配置，所以我们需要忽略掉这个配置，而采用webstrom的格式化默认的缩进，于是，同样还是在  `eslintrc.js` 中增加配置：

![image_1b5uauugrgmj12gee36hef1a3m3u.png-45.1kB][12]

此外，还有可能会报这种错：

![image_1b5uaeoib1uslo471rt61fig1q2d34.png-23kB][13]

提示在文件的末尾有太多的空行，原因是我们在vue文件中有这样的书写：

![image_1b5ub2t411dr1hfd8en12mlda34b.png-8.6kB][14]

这多余的空格导致报错。
删除掉这些空格后，webpack自动编译，错误也就不再存在了。

最后，我们回到 `src/main.js` 这个文件，会发现这个文件中有这么一段注释：

![image_1b5ub8jmslrp1lh8ktd1gte1hn4o.png-11.2kB][15]

这段注释有什么用呢？删除这段注释，编译会报错：

![image_1b5ubcs3pc1h9f6eglv1g5955.png-21.6kB][16]

这是什么原因呢？我们会发现，平时我们在写vue实例时，通常需要将其赋值给一个变量，就像这样：

```
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

倘若我们不想将其赋值给一个变量，就需要加这段注释了，这段注释可以跳过 `eslint` 对 `no-new` 的校验，然后就能这样写了：

```
/* eslint-disable no-new */
  new Vue({
    el: 'body',
    components: { App }
  });
```


---

# 4 webstorm新建文件模板

找到设置 `File and Code Templates`, 按照如下方式设置即可：

![image_1b5ubv61n19ebbfmcagorjenq5i.png-169.8kB][17]


# 5 stylus-loader 踩坑经历

找不到 `stylus-loader`:

    Error: Cannot resolve module 'stylus-loader'
![image_1b5uc7lr3janh4liqduvk3sf5v.png-24kB][18]

很明显，在我们的 `package.json` 中找不到 `stylus-loader`， 因此，我们需要手动安装。
打开 [stylus-loader][19] 的github首页，打开其 `package.json` 查看版本号：

![image_1b5ucv61n1769190p1iidrl91ui66c.png-85.7kB][20]

然后，在我们的中加入：

![image_1b5ud1kdek2l1llk1leu6n31mj06p.png-47kB][21]

然后 `npm install` 即可;
值得注意的是，这个 `^` 是指安装当前版本以后的最新版本。

然而，再次编译居然还是出现如下警告：

    npm WARN stylus-loader@2.4.0 requires a peer of stylus@>=0.52.4 but none was installed.
![image_1b5udg8go1lq0dlg1mmn9urfif76.png-51.3kB][22]

在 运行 `npm run dev` 时，报错：

    ERROR in Cannot find module 'stylus'
![image_1b5udjb00h316ke15i118t91u317j.png-23.6kB][23]

按照警告在package.json 中再加一个插件：

```
"stylus": "0.52.4",
```

再执行安装后就没有报错了，可以开始写 `stylus` 啦!

---

# 6 Vue组件命名 踩坑经历

我们定义了一个名为 header 的组件，所有的代码风格均正确，编译也通过，但是在页面中就是没有显示 header 组件的内容。打开chrome的控制台，会发现：

    VM249:1137 [Vue warn]: Do not use built-in or reserved HTML elements as component id: header
![image_1b5udsh19kpm16sa1rkt1pld1t3880.png-10.9kB][24]

找到原因了，因为 header 是 html 的一个保留关键字 = =

本来我们注册 `header` 是这样写的：
```
  export default({
    components: {
      header
    }
  });
```
现在统一改成这个样子：

```
  export default({
    components: {
      'v-header': header
    } 
  });
```

# 7 移动端经典的flex布局

最终效果图：
![image_1b5uesa4v134gfsabvovgadvc8d.png-17.3kB][25]

HTML代码：
```
    <div class="tab">
      <div class="tab-item">商品</div>
      <div class="tab-item">评论</div>
      <div class="tab-item">商家</div>
    </div>
```
CSS（Stylus）代码：
```
.tab
    display flex
    height 40px
    line-height 40px
    width 100%
    .tab-item
        flex: 1
        text-align center
```

当然，这只是实现了最基础的骨架结构，倘若想要加上功能和样式，还需要结合 `vue-router`，这个后面再说。



# 8 vue-router 踩坑经历

### 1 安装

尽管当前 vue2.0 和 vue-router 2.0 都已经发布，但本文还是从 vue1的全家桶开始踩坑。

这是1.0版本的分支：https://github.com/vuejs/vue-router/tree/1.0

注意了，安装时，是安装在 `dependencies` 而不是 `devDependencies` :


关于npm安装的帮助，可以通过下述命令行进入：

```
npm help install
```

安装指定版本可以通过以下指令：

```
npm install XXX@0.1.1
```

指定版本范围可以：

```
npm install XXX@">=0.1.0 <0.2.0"
```

所以，我们可以采用以下命令行安装 `vue-router`

```
npm install vue-router@">=0.7.13" --save
```

但是这样，我们将会安装 2.1.1 的版本，

![image_1b5ugk37sjta1u3bn2imsk1qnt97.png-2.9kB][26]

因为，我们还要对版本号限制，首先运行 `npm remove vue-router` 删除当前的 2.1.1 版本，再运行下述指令安装：

```
npm install vue-router@">=0.7.13 <2.0.0" --save
```
然后，打开 `package.json`, 会发现供 `vue1.0` 使用的 `vue-router` 已经成功被安装了：

![image_1b5ufs3en136tl93jeb1l6l5q8q.png-12.1kB][27]


### 2 使用

接下来，开始学习如何使用 `vue-router`,
这是 vue-router 0.7.13 的官方文档:

[0.文档首页][28]
[1.安装][29]
[2.基本用法][30]
[3.嵌套路由][31]
[4.路由规则和路由匹配][32]
[5.具名路径][33]
[6.路由配置项][34]
[7.`<router-view>`][35]
[8.`v-link`][36]
[9.动态组件载入 lazy load][37]
[10.切换控制流水线][38]
[11.API索引][39]

在 `main.js` 中导入 `vue-router` 并使用 use 方法安装 :

```
import Vue from 'vue';
import VueRouter from 'vue-router';
import App from './App';

/* 采用npm安装，需要手动安装vue-router */
Vue.use(VueRouter);
```

接下来的坑会比较多 = =

一开始，我们是直接把 App 这个根组件挂载到 index.html，但是这样做，就会和 vue-router 官方给出的写法不一致，因此，我们需要把 index.html 中的 
```
<app></app>
```
改成
```
<div id="app"></div>
```
路由的基本使用技巧是：
```
import Vue from 'vue';
import VueRouter from 'vue-router';
import App from './App';
import goods from './components/goods/goods';
import ratings from './components/ratings/ratings';
import seller from './components/seller/seller';

/* 采用npm安装，需要手动安装vue-router */
Vue.use(VueRouter);

/* 通过 extend 方法加载组件 */
let app = Vue.extend(App);

/* 创建一个路由实例，这里还可以对路由进行配置，这个以后再提 */
let router = new VueRouter();

/* 配置路由 */
router.map({
  '/goods': {
    component: goods
  },
  '/ratings': {
    component: ratings
  },
  '/seller': {
    component: seller
  }
});

/* 选择挂载点 */
router.start(app, '#app');
```

然后，我们偶尔会遇到这样一个警告：

    vue.common.js?e881:1137[Vue warn]: Attribute "id" is ignored on component <div> because the component is a fragment instance: http://vuejs.org/guide/components.html#Fragment-Instance

![image_1b5ujnee97qmbgl14t516sicu89k.png-15.7kB][40]

翻译过来就是：**属性“id”在组件<div>上被忽略，因为组件是一个片段实例**

也就是说，肯定有个组件的 `template` 没有根元素（这和 `Angular` 定义指令的规则不是一样的吗？（不过Angular会直接报错，而不是警告= =））

找到问题的出错点，就是在APP.vue中，删除掉根div了----
![image_1b5uk2qodqvh1c3abdc1m3g1sota1.png-54kB][41]

补上去，报错消失，喜大普奔~~~


---

### 3 解决相对路径的坑

在 `src/main.js` 中，我们之前一直都是这样写路径，但是我们希望能够把路径写得更干脆，所有的直接以 `'components'` 开头，那么该怎么做呢？

![image_1b5ungk88vp11uhf1gso9ki1d4b9.png-18.2kB][42]

在 `build/webpack.base.conf` 配置文件中：

![image_1b5unlu651bpc10a237rmbaqoum.png-26.2kB][43]

找到 `rescolve`:

![image_1b5uno86kgiurc31skb17i81td913.png-31.7kB][44]

也就是说，我们给 components 目录起了一个别名，它就叫 components，因此，我们在 `src/main.js` 中写路径都可以统一改成：

![image_1b5unru161os1s0lbr845t1r201g.png-14.8kB][45]

这种路径的编写风格是不是更加清爽呢？


---

# 9 CSS书写规范

```
.tab
    display flex
    width 100%
    height 40px
    line-height 40px
    .tab-item
      flex 1
      text-align center
      & > a
        display block
        font-size 14px
        color rgb(77, 85, 93)
```
通过这段stylus代码我们可以发现，平时在写css代码时，首先应该先写布局（不可配置的），写完布局再来写颜色、字体等这些可配置的选项。这样的话，css整体将会看起来比较清晰。

---

# 10 vue-router active属性

通常来说，在激活的 **router** 标签上，我们需要修改其颜色，以区分和未激活的路由。回顾在ng1中，我曾写过很多自定义指令来实现这一功能，但是，**vue-router** 给了我们一个很便捷的机会来实现这个需求。

通过生成的DOM我们可以发现，vue为我们每个激活的路由标签增加了一个属性，如下：

![image_1b5vjrf3m1iok1h3r7810ue53q9.png-26.4kB][46]

但是，这个class是不是太长了，事实上，这个class也是可配置的，打开 [路由配置][47] 文档，我们可以找到我们想要的配置属性：

![image_1b5vk9d6211ql1ho1d0n1ol11evfm.png-56.2kB][48]


---

# 11 实现多设备预览

前端开发同学常常会问，PC开发，手机如何实时预览呢？

在我刚刚接触前端的时候，我的做法是把代码丢在tomcat上面，事实上，远没有必要这么复杂。

## VUE开发模式

由于vue+webpack已经帮我们构建好了服务器，所以思路很简短

查看我们域名：
```
http://localhost:8080/#!/seller
```
在`cmd/shell`中键入`ipconfig`找到自己本机的ip：

![image_1b5vknrl3196q10g910po1lel12gd13.png-34.5kB][49]

然后由此得到我们在手机上访问的路径：

```
http://192.168.1.104:8080/#!/seller
```
注意，前提是手机和电脑处于同一局域网内。

## webstrom开发

如果不是开发vue，或者说开发的项目的脚手架没有那么贴心呢？这时候就需要采用webstrom本身强大的功能了。

从webstrom打开的网页往往具有以下模式：
```
localhost://63342 xxxxxxxx
```
直接按照上述vue修改为本机ip的方式是不行的，可以按照以下方法实现：

![image_1b761jtq1ema1kqc18mja9pua99.png-62.3kB][50]

记住，一定要勾选可以接受外部设备的链接。


---

# 12 1px像素边框实现



在手机上打开网页后，我们会发现，我们通过
```
border: 1px solid rgba(7, 17, 27, 0.1)
```
根本无法实现真实的1px像素边框。


### 1px边框实现的思路

给需要加边框的元素添加一个伪类 after
在after伪类中设置为一条线
然后在dpi为2/3的手机上通过media query来对线的宽度缩放。






  [1]: https://docs.angularjs.org/
  [2]: http://cn.vuejs.org/
  [3]: https://cn.vuejs.org/images/components.png
  [4]: http://static.zybuluo.com/a472590061/qshobbnz0y1kat2sbc0ix8td/image_1b5uab3k6g227i41an5mp6u62a.png
  [5]: http://static.zybuluo.com/a472590061/ua7b8rkpqzl92qwbo26kzamr/image_1b5u9is1868f2iq5ch1r9715lgm.png
  [6]: http://static.zybuluo.com/a472590061/sauinmt6o4wrj50d1za9txvj/image_1b5ua640q7ti135lgncs0usqc13.png
  [7]: http://static.zybuluo.com/a472590061/clj6t4i09n089nuazeebp00d/image_1b5ua9r9eqt21cgocvb2371l4a1t.png
  [8]: http://eslint.org/docs/rules/semi
  [9]: http://static.zybuluo.com/a472590061/uzsw375kx1d06tstq6zqmkgl/image_1b5uacspsvs4f0f2s71rk71ccq2n.png
  [10]: http://static.zybuluo.com/a472590061/rubuniscvkoc97x86x2zr16o/image_1b5uakob31321m8d69ooe7t3i3h.png
  [11]: http://eslint.org/docs/rules/indent
  [12]: http://static.zybuluo.com/a472590061/9uzhw782hffl8uh1yzh71nlo/image_1b5uauugrgmj12gee36hef1a3m3u.png
  [13]: http://static.zybuluo.com/a472590061/96r20kzcb86dtrwewt9ngv7m/image_1b5uaeoib1uslo471rt61fig1q2d34.png
  [14]: http://static.zybuluo.com/a472590061/q17x428bl5w3fwlxxg27jzzd/image_1b5ub2t411dr1hfd8en12mlda34b.png
  [15]: http://static.zybuluo.com/a472590061/5v48zxvnlo7wvpc4zf4v2qoj/image_1b5ub8jmslrp1lh8ktd1gte1hn4o.png
  [16]: http://static.zybuluo.com/a472590061/uzdv8rafjdypicw2hx77mvtp/image_1b5ubcs3pc1h9f6eglv1g5955.png
  [17]: http://static.zybuluo.com/a472590061/61kt9apaecxmsctkd4qao60l/image_1b5ubv61n19ebbfmcagorjenq5i.png
  [18]: http://static.zybuluo.com/a472590061/aelwn0mpbduq7u5cxlvyi7l9/image_1b5uc7lr3janh4liqduvk3sf5v.png
  [19]: https://github.com/shama/stylus-loader
  [20]: http://static.zybuluo.com/a472590061/0xoxawnlg0vjmi9j3jx05920/image_1b5ucv61n1769190p1iidrl91ui66c.png
  [21]: http://static.zybuluo.com/a472590061/gegfr6zn44szsamhz4j4dc4m/image_1b5ud1kdek2l1llk1leu6n31mj06p.png
  [22]: http://static.zybuluo.com/a472590061/pbvai15n7qjkhysahm9elzqx/image_1b5udg8go1lq0dlg1mmn9urfif76.png
  [23]: http://static.zybuluo.com/a472590061/q3jhsn6skpldo714myciyoon/image_1b5udjb00h316ke15i118t91u317j.png
  [24]: http://static.zybuluo.com/a472590061/9677i8lnhsq6e30nrrjzbanr/image_1b5udsh19kpm16sa1rkt1pld1t3880.png
  [25]: http://static.zybuluo.com/a472590061/tcc3m3216w3bzbbl2t09763o/image_1b5uesa4v134gfsabvovgadvc8d.png
  [26]: http://static.zybuluo.com/a472590061/0ejpoj0aczq1f20h4kb0jcrd/image_1b5ugk37sjta1u3bn2imsk1qnt97.png
  [27]: http://static.zybuluo.com/a472590061/x0yal7mxw1p95k7veoggo6re/image_1b5ufs3en136tl93jeb1l6l5q8q.png
  [28]: https://github.com/vuejs/vue-router/tree/1.0/docs/zh-cn
  [29]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/installation.md
  [30]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/basic.md
  [31]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/nested.md
  [32]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/route.md
  [33]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/named.md
  [34]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/options.md
  [35]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/view.md
  [36]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/link.md
  [37]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/lazy.md
  [38]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/pipeline/README.md
  [39]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/api/README.md
  [40]: http://static.zybuluo.com/a472590061/pk6b15mf77a1wjsqs3o14pf2/image_1b5ujnee97qmbgl14t516sicu89k.png
  [41]: http://static.zybuluo.com/a472590061/hj04tyxaef6v3c8xnk5l6mhe/image_1b5uk2qodqvh1c3abdc1m3g1sota1.png
  [42]: http://static.zybuluo.com/a472590061/06kz565yn7ufxgtrzal4crtt/image_1b5ungk88vp11uhf1gso9ki1d4b9.png
  [43]: http://static.zybuluo.com/a472590061/84pviw3i6nthkkvs010pqgho/image_1b5unlu651bpc10a237rmbaqoum.png
  [44]: http://static.zybuluo.com/a472590061/eyi8mcfqsgexla8w1g5w8p7l/image_1b5uno86kgiurc31skb17i81td913.png
  [45]: http://static.zybuluo.com/a472590061/qlihse7i6f1jdx0ysy86vl9d/image_1b5unru161os1s0lbr845t1r201g.png
  [46]: http://static.zybuluo.com/a472590061/152fotaya05pfvc3m1ns6e2j/image_1b5vjrf3m1iok1h3r7810ue53q9.png
  [47]: https://github.com/vuejs/vue-router/blob/1.0/docs/zh-cn/options.md
  [48]: http://static.zybuluo.com/a472590061/p6p9pp1wm6xwjuqrxboqot7l/image_1b5vk9d6211ql1ho1d0n1ol11evfm.png
  [49]: http://static.zybuluo.com/a472590061/b9c3vah7cueylnrjcjhev32d/image_1b5vknrl3196q10g910po1lel12gd13.png
  [50]: http://static.zybuluo.com/a472590061/odi5a4ngwyuq7b4m9dbtx7lv/image_1b761jtq1ema1kqc18mja9pua99.png