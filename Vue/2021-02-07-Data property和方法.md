### Data property

组件的data选项是一个函数，vue在创建新组件时调用此函数。它应该返回一个对象，然后vue会通过响应性系统将其包裹起来，并以$$data的形式将其存储在组件实例中。为方便起见，该对象的任何顶级property也直接通过组件实例暴露出来：

```js
const app=Vue.createApp({
  data() {
      return { count:4 }
  }  
})

const vm=app.mount('#app')

console.log(vm.$data.count) // => 4
console.log(vm.count)  // => 4

// 修改 vm.count的值也会更新$data.count
vm.count=5
console.log(vm.$data.count) // =>5

// 反之亦然
vm.$data.count=6
console.log(vm.count)  // => 6
```

这些实例property仅在实例首次被创建时添加，所以你需要确保它们都在data返回的对象中。必要时，需要给尚未提供所需值的property赋个初始值 null、underdefined之类的。

可以将不包含在data中的新property添加给组件实例，但是这样该property的不在背后的响应式$data里，所以vue的响应式系统就不会跟踪它们。

vue使用 $前缀通过组件实例暴露自己的内置API，并且为内部property保留 _前缀。我应该避免使用这两个字符开头的顶级data property名称。

### 方法

使用methods选项向组件中添加方法，它是一个包含所需方法的对象：

```js
const app=Vue.createApp({
    data(){
        return {count: 4 }
    },
    methods(){
        increment(){
            this.count++
        }
    }
})

const vm=app.mount('#app')
console.log(vm.count) // =>4
vm.increment()
console.log(vm.count) //=>5
```

vue自动为methods绑定this，以便于它始终指向组件实例，确保方法在用作事件监听或回调时保持正确的this指向。在定义methods时应避免使用箭头函数，因为这会阻止vue绑定恰当的this指向。

这些methods和组件中的其他property一样，可以在组件的模板中被访问。在模板中，它们通常被当作事件监听使用：

```html
<button @click="increment">
     Up vote
</button>
```

也可以直接从模板中调用方法。虽然通常这时换成计算属性会更好，但是在计算属性不可用时，使用方法可能会很有用。我可以在模板支持JS表达式的任何地方调用方法。

```html
<span :title="toTitleDate(date)">
	{{formatDate(date)}}
</span>
```

如果toTitleDate和formatDate访问任何响应式数据，则将其作为渲染依赖项就行跟踪，就像直接在模板中使用过的一样。

在模板中调用方法不应该更改数据，如果要这样做的话，可以使用生命周期钩子。



#### 防抖和节流

vue没有内置支持防抖和节流，但可以使用lodash实现。可以在created生命周期函数里添加该防抖函数：

```js
app.component('save-button',{
    created() {
        // 用Lodash的防抖函数
        this.debouncedClick=_.debounce(this.click, 500)
    },
    unmounted(){
        // 移除组件时，取消定时器
        this.debouncedClick.cancel()
    },
    methods: {
        click(){
            // 响应点击
        }
    }, 
    template: `
		<button @click="debouncedClick">
			Save
		</button>
	`
})
```









































