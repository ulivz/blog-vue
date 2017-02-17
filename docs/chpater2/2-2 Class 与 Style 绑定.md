# Vue Class 与 Style 绑定

# Class （ v-bind:class）

**对象语法**

```
    <div id="class-demo" v-bind:class="{ active: isActive, 'text-danger': hasError }">
    {{ msg }}
    </div>
    
    var classDemo = new Vue({
    el: '#class-demo',
    data: {
        isActive: false,
        hasError: false,
        msg: '测试Class绑定'
    }
})
```
**数组语法**
```
<div v-bind:class="[activeClass, errorClass]">

data: {
  activeClass: 'active',
  errorClass: 'text-danger'
}
```

在数组语法中使用对象语法：
```
<div v-bind:class="[{ active: isActive }, errorClass]">
```

# Style（ v-bind:style）

**对象语法**

```
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>

data: {
  activeColor: 'red',
  fontSize: 30
}
//直接绑定到一个样式对象通常更好，让模板更清晰
```

**数组语法**
```
<div v-bind:style="[baseStyles, overridingStyles]">
```
**自动添加前缀**

当 v-bind:style 使用需要特定前缀的 CSS 属性时，如 transform ，Vue.js 会自动侦测并添加相应的前缀。