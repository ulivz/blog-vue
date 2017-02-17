# （1）Vue 基础

## 1 基本使用
### 1.1 声明式渲染 - 数据绑定
```
<div id="app">
  {{ message }}
</div>
```
```
//要注意的是，每一个采用了Vue语法的DOM树
//必须给给DOM树的根节点设置一个id
//并根据这个根节点的id进行实例化 --- new Vue({ ... })
//实例化后，该DOM树中的每个子元素都可以访问实例对象中的data
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
数据和 DOM 被绑定在一起，所有的元素都是**响应式**的。
可以通过浏览器的控制台来修改data来验证。

---
### 1.2 v-bind 绑定DOM属性
```
  <span v-bind:title="message"> CHL </span>
```
简写形式：
```
  <span :title="message"> CHL </span>
```
---

### 1.3 v-if 条件
```
  <p v-if="seen">Now you see me</p>
```
---
### 1.4 v-for 条件
```
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
```
---
### 1.5 v-on 监听事件
```
 <button v-on:click="reverseMessage">Reverse Message</button>
```
---
### 1.6 组件入门

```
Vue.component('todo-item', {
  template: '<li>This is a todo</li>'
})
```
---

## 2 Vue实例
### 2.1 每个Vue 实例都会代理其 data 对象里所有的属性
```
var data = { a: 1 }

//这个Vue示例代理了 data 对象上的所有属性
var vm = new Vue({
  data: data
})
// 设置属性也会影响到原始数据; （vm.a 会影响 data.a）
// ... 反之亦然; （data.a 会影响 vm.a）
```
注意一点，**只有这些被代理的属性是响应的**。
如果在实例创建之后添加新的属性到实例上，那么它不会触发视图更新。

---
### 2.2 Vue实例暴露的实例属性与方法
```
var data = { a: 1 }
var vm = new Vue({
  el: '#example',
  data: data
})
//这就是你想要的data
vm.$data === data // -> true

//这就是你想要的DOM对象
vm.$el === document.getElementById('example') // -> true

// $watch 是一个实例方法
vm.$watch('a', function (newVal, oldVal) {
  // 这个回调将在 `vm.a`  改变后调用
})
```

---

## 3 模板语法
### 1.1 Mustache语法 {{   }}

双大括号 `{{ }}` 会将数据解释为纯文本；
为了输出真正的 HTML ，你需要使用 `v-html` 指令：
注意，绑定属性，请使用 `v-bind`

---
### 1.2 在Mustache中写JS
```
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```
注意，每个绑定**只能把包含单个表达式**
此外，模板表达式并不位于全局空间，不要试图访问用户定义的全局变量。当然，全局变量有一个白名单，例如`Math `和` Date`

---
### 1.3 过滤器
```
{{ message | capitalize }}
```
```
new Vue({
  // ...
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```
注意，过滤器可以串联，可以接受参数
```
{{ message | filterA('arg1', arg2) }}
```
这里，字符串 'arg1' 将传给过滤器作为第二个参数， arg2 表达式的值将被求值然后传给过滤器作为第三个参数。

---

### 1.4 指令

这里简单汇总一下Vue的指令：

指令名 | 作用 | 对比AngularJS
--- | ---| ---
v-text | 更新元素的textContent，更新部分请使用`{{ }}` | ng-bind，注意，ng-bind=“a”，a是一个变量
v-html | 更新元素的innerHTML,注意，不得插入Vue模板 | ng-bind-html
v-if | 根据条件销毁或重建元素 | ng-if
v-show | 根据表达式之真假值，切换元素的 display CSS 属性 | ng-show
v-else | 前一兄弟元素必须有 v-if | 无相似指令
v-for |　可以渲染Array/Object/number/string,注意，不仅可以得到值，还可以得到数组的索引或者对象的键 | ng-repeat
**v-on** | 缩写`@`，用于监听事件，注意可以带**修饰符**　| -
**v-bind** | 缩写`:`，用于绑定class、style、prop等，在绑定 prop 时，prop 必须在子组件中声明　| -
v-model |  用于双向绑定 | ng-model
v-pre | 跳过这个元素和它的子元素的编译过程。| -
v-cloak | 不会显示，直到编译结束。| ng-cloak
v-once | 只渲染元素和组件一次 | -


---


## 3 指令详述
### 3.1 计算属性（computed）

（1）若计算属性 A 依赖于data B ( A=fx(B) ), 那么当 B 改变时， A 也会跟随改变

（2）计算属性默认只有`getter`，但在需要时也可以提供一个`setter`
```
// ...
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
// ...
```


---
### 3.2 监听（watch）

创建方法，在Vue示例中，创建一个： 
```
watch: {
    value1: function(newValue){
        /* ... */
    },
    value2: function(newValue){
        /* ... */
    },
}
```
---
### 3.3 Class 与 Style 绑定

#### **3.3.1 Class**

动态class:

```
<div v-bind:class="{ active: isActive }"></div>
/* 表示 class active 的更新将取决于数据属性 isActive 是否为真值 */
```

class对象：
```
<div class="static"
     v-bind:class="{ active: isActive, 'text-danger': hasError }">
</div>

data: {
  isActive: true,
  hasError: false
}
```
class对象也可以这样玩：

```
<div v-bind:class="classObject"></div>

data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}
```
class对象也可以是一个计算属性:
```
/* 没有例子，自行想象 ^ ^ */
```

class数组：
```
<div v-bind:class="[activeClass, errorClass]">

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

class与三元表达式：

```
<div v-bind:class="[isActive ? activeClass : '', errorClass]">
```

给组件加class：

```
/* 这样写，是直接给template的根元素加class */
<my-component class="baz boo"></my-component>

/* 这样写，便是动态class  */
<my-component v-bind:class="{ active: isActive }"></my-component>
```

---

#### **3.3.2 内联样式**

对象：
```
/* 直接将v-bind:style绑定到一个对象就好啦 - - */
/* CSS 属性名可以用驼峰式（camelCase）或短横分隔命名（kebab-case）^ ^ */
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

data: {
  activeColor: 'red',
  fontSize: 30
}
/* 为什么不直接写成一个对象，mdzz... */
```
数组：
```
/* 天呐，这就不说了吧 = = */
<div v-bind:style="[baseStyles, overridingStyles]">

```

最后提醒一点，当 v-bind:style 使用需要特定前缀的 CSS 属性时，如 transform ，Vue.js 会自动侦测并添加相应的前缀。


---
### 3.4 条件渲染（v-if）

又说到这个 = =, 来个小范例：
```
<h1 v-if="ok">Yes</h1>
<h1 v-else>No</h1>
```

注意，要切换多个元素，请用`<template>`作root element，哦，对了，`<template>`不支持`v-show` ^ ^
还有，v-else这个梗儿，虽然没什么用，但你一旦用到了，记得放到v-if 或 v-show 元素的后面 - -

关于`v-if`和`v-show`的区别的区别，不要问我，去看看`ng-if`和`ng-show`。

---
### 3.5 列表渲染（v-for）

基本用法就不讲了吧，`ng-repeat`用过吧- -

**数据类型**

还是留意一下可以迭代渲染的数据类型：

数组： 可以得到`(item, index) in items`
对象： `(val, key) in object`或`(val, key, index) in object`
数字：天呐，这也可以，其实就是重复N次 - -
string：雾草，这...我还没试过

注意一点吧，如下：

- 在遍历对象时，是按 Object.keys() 的结果遍历，但是不能保证它的结果在不同的 JavaScript 引擎下是一致的。（天呐，我竟不懂。。我只知道，有些元素你是遍历不到的^ ^ **比如某个元素已经被配置成 不可枚举^ ^**,请看 《你不知道的JS》`P111`）

---
#### **3.5.1 v-for和组件**

这个，有点难。直接看示例 ^ ^

注意几个点：
**给子组件传值不是一般要用props吗？**
那么你要给子组件传值，在父组件中，采用:
```
v-bind:someProps = '(item, index) in dataArray'
```
在子组件中定义：`props:['someProps']`,就可以用`someProps`了...是不是很简单？

**给父组件传值不是一般要用$emit吗？**
那么，在子组件中，这样玩：比如在template中有一个button
```
'<button v-on:click = "$emit( \'someEventName\' )"> text </button>'
```
那么你在父组件中就可以这样监听这个事件:
```
v-on:someEventName = "someMethodName"
```
是不是也很简单^ ^

当然，**上述的例子并没有实现向父组件传值，而只是给父组件传递了一个可供监听的事件。**，如何传值呢？，请看下例：

我们在子组件内(名为`first-component`)的一个`methods`（这个方法由子组件的`template`调用，其实就和上个例子一样，只是我们把方法抽出来，写在了`methods`里面）中，写了这么一句：
```
this.$emit('isclick', {
     message: event.target.previousSibling.innerText,
     count: this.count,
})
```
那么，在监听`isclick`这个时间时，如何得到后面跟随的一个对象呢？答案如下：
```
<first-component :prop-msg="propMsg" v-on:isclick=" myCount = $event ">
    {{myCount}}
</first-component>
```

对，没错！ 这个`$event`就是你所传递的对象，哇塞，居然是这样- -

---
#### **3.5.2 key**
```
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```



