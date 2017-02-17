# Vue.js 项目实战踩坑笔记（二）


# 1 解决Webpack没有自动刷新

在采用Webstorm进行开发时，我们都知道文件会自动保存，但是，这种“安全写入”的机制，会使得文件的变化首先会首先保存到临时文件中，因此，这就阻挡了`vue-hot-loader`发挥功效。

为了解决这个问题，我们需要手动修改WS的设置，如下：

![image_1b76664a81eo56bq10f05a2kgn9.png-97.7kB][1]


  [1]: http://static.zybuluo.com/a472590061/5gf590tg56dgtvwz5n3ubsr5/image_1b76664a81eo56bq10f05a2kgn9.png
  
 
# 2 CSS Stickey Footer布局

简单来说，`CSS Stickey Footer布局` 就是如果页面内容不够长的时候，页脚块粘贴在视窗底部；如果内容足够长时，页脚块会被内容向下推送。


# 3 Better-Scroll 的使用

首先，引入BScroll：
```
  import BScroll from 'better-scroll';
```
接着，给需要添加BScroll的DOM节点添加v-el：
```
// 注意，这是vue 1.0的写法
v-el:meau-wrapper
```
一般来说，初始化BScroll需要等待DOM渲染完成,这里Vue给我们提供了一个很好的API：`$nextTick`(将回调延迟到下次 DOM 更新循环之后执行)。

一般来说，我们可以在created()中调用this.$nextTick，然后在其回调中调用BScroll的方法：

```
this.menuScroll = new BScroll(this.$els.meauWrapper, {
  click: true
});
```

# 4 Better-Scroll 的使用