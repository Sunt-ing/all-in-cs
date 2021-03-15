元素的class列表和内联样式都是attribute，可以使用v-bind操作它们。

#### 绑定HTML class

##### 对象语法

我们可以传给 :class 一个对象，以动态地切换class：

```html
<div :class="{active: isActive}"> </div>
```

- active这个class存在与否取决于isActive 这个property

可以在对象中传入更多的字段来动态地切换多个class，另外，:class 命令也可以和普通class attribute共存。当有如下模板：

```html
<div
     class="static"
     :class="{active: isActive, 'text-danger':hasError }"
></div>
```

和如下data

```js
data(){
	return {
        isActive: true,
        hasError: false
    }
}
```

渲染的结果为：

```html
<div class="static active"> </div>
```

当isActive或hasError有变化时，class列表将相应的更新。例如，如果hasError的值为true，class列表将变为"static active text-danger"

绑定的数据对象不必内联定义在模板里：

```html
<div :class="classObject"> </div>
```

```js
data(){
	return {
        classObject:{
            active:true,
            'text-danger':false
        }
    }
}
```

渲染结果和上面一样。我们也可以在这里绑定一个返回对象的计算属性：

```html
<div :class="dataObject" > </div>
```

```js
data() {
    return {
        isActive: true,
        error: null
    }
},
computed: (){
    classObject(){
        return {
            active: this.isActive && !this.error,
            'text-danger':this.error && this.error.type ==='fatal'
        }
    }
}
```

##### 数组语法

可以把一个class列表传给 :class ：

```html
<div :class="[activeClass, errorClass]"> </div>
```

```js
data() {
    return {
        activeClass: 'active',
        errorClass: 'text-danger'
    }
}
```

渲染的结果为：

```html
<div class="active text-danger"> </div>
```

可以用三元表达式切换列表中的class：

```html
<div class="[isActive ? activeClass: "", errorClass]"></div>
```

- 这样写只有在isActive为true时才添加activeClass，但是始终添加errorClass。

但是，在有多个条件时这样写比较繁琐，可以在数组中使用对象语法：

```html
<div  :class="[{active:isActive }, errorClass]"></div>
```

##### 在组件上使用

当我在带有单个根元素的自定义组件上使用class attribute时，这些class将被添加到该元素中。此元素上的现有class将不被覆盖。例如：你声明了这个组件：

```js
const app=Vue.createApp({})

app.component('my-component',{
    template: `<p class="foo bar">Hi!</p>`
})
```

然后在使用它的时候添加一些class：

```html
<div id="app">
    <my-component class ="baz boo"></my-component>
</div>
```

HTML将被解析成：

```html 
<p class="foo bar baz boo">Hi</p>
```

也适用于带数据绑定的 class ：

```html 
<my-component :class="{active: isActive}"></my-component>
```

当isActive为true时，HTML将被渲染成：

```html 
<p class ="foo bar active">Hi</p>
```

如果我的组件上有多个根元素，那我需要决定哪些部分将接收这个类。可以使用$attrs组件属性执行此操作：

```html
<div id="app">
     <my-component class="baz"></my-component>
</div>
```

```js
const app =Vue.createdApp({})

app.component('my-component', {
    template: `
		<p :class="$attrs.class">Hi</p>
		<span> This is a child component </span>
    `
})
```

#### 绑定内联样式

##### 对象语法

:style的对象语法很直观——看着像CSS，但其实是一个JS对象。CSS property的名称可以用驼峰式或短横线分隔（kebab-case，要用引号括起来）来命名：

```html
<div :style="{color:activeColor, fontSize: fontSize+'px'}" ></div>
```

```js
data(){
    return {
        activeColor: 'red',
        fontSize: 30
    }
}
```

直接绑定到一个样式对象上会让模板更清晰：

```html
<div :style="styleObject"></div>
```

```js
data(){
    return {
        styleObject: {
            color: 'red',
            fontSize: '13px'
        }
    }
}
```

##### 数组语法

:style 的数组语法可以将多个样式应用到同一个元素上：

```html
<div :style="[baseStyles, overridingStyles]"></div>
```













































