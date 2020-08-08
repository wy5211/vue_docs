# watch 监听（重点）

```html
<div id="app">
    <input v-model="a" /> + <input v-model="b" /> = <span>{{c}}</span>
</div>
<script>	
var app = new Vue({    
    el: '#app',
    data: { a:1, b:2, c:3 },
    watch: {	
        a(val){ this.c = Number(val) + Number(this.b); },
        b(val){ this.c = Number(this.a) + Number(val); }
    }
})    
</script>
```

# computed 计算属性（重点）

```html
<div id="app">
    <input v-model="a" /> + <input v-model="b" /> = <span>{{c}}</span>
</div>
<script>	
var app = new Vue({    
    el: '#app',
    data: { a:1, b:2 },
    computed: { 
        c:function(){return Number(this.a)+Number(this.b);
    }
})    
</script>
```

## 计算属性缓存

注意：视图层不建议使用methods函数，该需求应该用计算属性代替。

```html
<div id="app">
    {{str}}
    <input type="text" v-model="a" />
    +
    <input type="text" v-model="b" />
    =
    {{c}} <br> {{d()}}
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            str : "加法运算",
            a : 1,
            b : 2
        }
    },
    computed:{  // 推荐用计算属性 (因为计算属性有缓存)
        c(){
            console.log('c 执行了');
            return Number(this.a) + Number(this.b);
        }
    },
    methods:{   // 不推荐用方法
        d(){
            console.log('d 执行了');
            return Number(this.a) + Number(this.b);
        },
        e(){
            console.log('e 执行了')
        }
    }
})
// 执行 vm.str="abc" 时，这段代码与视图层的d()没有任何关系，但是d（）被执行了，
// 意味着只要视图发生变化，视图层的方法都会被执行
</script>
```

## setter和getter

注意：不建议使用setter和getter。

```javascript
var vm = new Vue({
    el:"#app",
    data(){
        return {
            a : 1,
            b : 2
        }
    },
    computed:{
        c : {
            get(){ // c被获取时执行的代码
                console.log('get');
                return this.a+this.b;
            },
            set(v){ // c被设置时执行的代码
                console.log('set:', v)
            }
        }
    }
})
```

```javascript
// 设置c
vm.c = 6;   // 自动执行 set()

// 获取c
vm.c;       // 发现不会自动触发 get()

// a是c的依赖，a变化就相当于c变化，c变化后系统会自动获取，显示到页面上。
vm.a = 123; // 自动触发c的 get()
```

## 计算属性参数

注意：不建议在计算属性中使用参数，该需求应该用filter过滤代替。

```html
<div id="app">
    {{a(5)}}
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            x : 2
        }
    },
    computed:{
        a(){
            return function(n){
                return n + this.x;
            };
        }
    }
})
</script>
```



# mixins 指混入

mixins 指混入，其实就是把对象合并了。

```
<script>
var mixin = {
	data: {
		msg: "混入"
	}
}
var app = new Vue({
	mixins: [mixin],
	el: "#app",
	template: "#root"
});
</script>
```



# 虚拟DOM和DIFF算法

虚拟DOM（Virtual dom），也就是我们常说的虚拟节点，它是通过JS的Object对象模拟DOM中的节点，然后再通过特定的render方法将其渲染成真实的DOM的节点。

```javascript
// 创建虚拟节点
var temp = document.createDocumentFragment();

for( var i=0; i<100; i++ ){
    var li = document.createElement('li');
    // 将li放入虚拟节点中，这一步操作不会产生重绘回流
    temp.appendChild(li);
    li.innerHTML = i;
}

// 真实节点的操作
ul1.appendChild(temp);
```

## 为什么要使用虚拟节点？

频繁的操作DOM，会大量的造成页面的重绘和回流，出于性能优化的考虑，我们应该减少重绘和回流的操作。

> 重绘：例如 div1.style.color='red' 这种代码，只能改变颜色，并不会影响其他元素的布局，这种操作被称为重绘。

> 回流：例如 div1.style.padding = '20px' 这种代码，会影响到其他元素的布局，这种操作被称为回流。

> 回流必将引起重绘，而重绘不一定会引起回流。比如：只有颜色改变的时候就只会发生重绘而不会引起回流。

对虚拟节点的DOM操作，并不会触发重绘和回流，把处理后的虚拟节点映射到真实DOM上，只触发一次重绘和回流。

## DIFF算法

DIFF算法是DOM更新的一种算法，指页面被更新时，程序用哪种策略去做更新DOM。


## 渲染函数 createElement()

```html
<div id="app">
    <abc></abc>
</div>
<script>
    Vue.component('abc', {
        render: function (createElement, context) {
            // createElement() 的返回值就是一个VNode(虚拟节点)
            return createElement('div', {style:{color:'green'}}, '你好')
        }
    })

    var app = new Vue({
        el: '#app'
    })
</script>
```

> createElement 到底会返回什么呢？其实不是一个实际的 DOM 元素。它更准确的名字可能是 createNodeDescription，因为它所包含的信息会告诉 Vue 页面上需要渲染什么样的节点，及其子节点。我们把这样的节点描述为“虚拟节点 (Virtual Node)”，也常简写它为“VNode”。“虚拟 DOM”是我们对由 Vue 组件树建立起来的整个 VNode 树的称呼。


createElement() 参数1为标签名，参数2是一个对象，参数3是子节点。

**createElement() 第二个参数的内容：**

```javascript
{
  // 和`v-bind:class`一样的 API
  // 接收一个字符串、对象或字符串和对象组成的数组
  'class': {
    foo: true,
    bar: false
  },
  // 和`v-bind:style`一样的 API
  // 接收一个字符串、对象或对象组成的数组
  style: {
    color: 'red',
    fontSize: '14px'
  },
  // 普通的 HTML 特性
  attrs: {
    id: 'foo'
  },
  // 组件 props
  props: {
    myProp: 'bar'
  },
  // DOM 属性
  domProps: {
    innerHTML: 'baz'
  },
  // 事件监听器基于 `on`
  // 所以不再支持如 `v-on:keyup.enter` 修饰器
  // 需要手动匹配 keyCode。
  on: {
    click: this.clickHandler
  },
  // 仅用于组件，用于监听原生事件，而不是组件内部使用
  // `vm.$emit` 触发的事件。
  nativeOn: {
    click: this.nativeClickHandler
  },
  // 自定义指令。注意，你无法对 `binding` 中的 `oldValue`
  // 赋值，因为 Vue 已经自动为你进行了同步。
  directives: [
    {
      name: 'my-custom-directive',
      value: '2',
      expression: '1 + 1',
      arg: 'foo',
      modifiers: {
        bar: true
      }
    }
  ],
  // 作用域插槽格式
  // { name: props => VNode | Array<VNode> }
  scopedSlots: {
    default: props => createElement('span', props.text)
  },
  // 如果组件是其他组件的子组件，需为插槽指定名称
  slot: 'name-of-slot',
  // 其他特殊顶层属性
  key: 'myKey',
  ref: 'myRef',
  // 如果你在渲染函数中向多个元素都应用了相同的 ref 名，
  // 那么 `$refs.myRef` 会变成一个数组。
  refInFor: true
}
```

例如：

```javascript
Vue.component('my-component', {
  render: function (h, context) {
    return h("h1", {
        "style":"color:red", 
        domProps:{
            innerHTML : "你好",
            title : "hello"
        }
    }, ["abc"])
  }
})
```





# component 组件（重点）

一个项目特别大，所有的功能都写在一起是不合适的，所以把功能抽象出来，形成一个个组件，最后把这些组件组合在一起。

## 全局组件

```javascript
Vue.component('my-component-name', {
    template:'<div>abc</div>'
})
```
```html
<my-component-name> </my-component-name>
```

## 局部组件

```
<div id="app"><hi></hi></div>
<script>
var app = new Vue({
	el: '#app',
	components: {
        'hi': {"template": "<h1>hi</h1>"}
	}
})
</script>
```

## prop 属性

使用组件时，把数据从组件的外面，传入到组件的内部，组件内使用props接收。

```
<btn v-bind:a="4"></btn>
props:['a']
```

> 注意：prop需要小写

单向数据流

所有的 prop 都使得其父子 prop 之间形成了一个单向下行绑定：

父级 prop 的更新会向下流动到子组件中，但是反过来则不行。

这样会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解。


## prop 验证

使用script标签引入vue.js文件这种方法测试props验证是无效的，需要用webpack开发环境测试。	

```
props: {
    propA: Number, // 基础的类型检查 (`null` 匹配任何类型)     
    propB: [String, Number], // 多个可能的类型    
    propC: {type: String, required: true}, // 必填的字符串 ，属性名称必须存在   
    propD: {type: Number, default: 100}, // 带有默认值的数字  ， 值为undefined时，默认值才生效
    // 带有默认值的对象    
    propE: {type: Object, default: function () {
        return { message: 'hello' }
    }},    
    // 自定义验证函数    
    propF: {validator: function (value) {
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
    }}
}
```

## 子页面向父页面传递数据	

子组件

```
const component2 = {
    template: `
        <div>component2 <button @click="fn">按钮</button> </div>
    `,
    methods:{
        fn(){
            this.$emit("abc", 2)
        }
    }
}
```

父组件

```
const component1 = {
    template: `
        <div>
            component1 
            <component2 @abc="fn2"></component2>
        </div>
    `,
    methods:{
        fn2(num){ alert(num) }
    },
    components: { component2 }
}
```




## 组件边界	
	
### 访问组件

- $root 指跟
- $parent 指父
- $children 指子
- $refs 当前页面中查找

```
<input ref="x"/>
<comp1 ref="y" />

this.$refs 能够找到当前页面中的ref定义
```

### 依赖注入

- provide （提供给后代组件的方法）
- inject（接收前代组件提供的方法）

例如abc组件中含有xyz组件，abc组件provide提供一个方法，xyz组件inject可以直接使用这个方法

```javascript
Vue.component('abc', {
    template:`
        <div>1<xyz></xyz></div>
    `,
    provide(){ return { abc:this.fn } },
    methods:{ fn(){alert()} }
})

Vue.component('xyz', {
    template:`
        <div><button @click='abc'>2</button></div>
    `,
    inject:["abc"] 
})      
```


## 组件切换

```
<div id="app">
    <template>
        <button type="button" @click="fn('aaa')">AAA</button>
        <button type="button" @click="fn('bbb')">BBB</button>
        <component v-bind:is="view"></component>	
    </template>
</div>
<script>
var app = new Vue({
    el: '#app', data: { view:"bbb" },
    methods: { fn(str){this.view=str} },
    components: {
        "aaa": {template:"<div>aaa</div>"},
        "bbb": {template:"<div>bbb</div>"},
        "ccc": {template:"<div>ccc</div>"}
    }
});
</script>
```

## keep-alive 缓存

在动态组件上使用 keep-alive

组件实例能够被在它们第一次被创建的时候缓存下来

> 主要用于保留组件状态或避免重新渲染。

*keep-alive 的组件必须有名字*

```
<div id="app">
    <template>
        <button type="button" @click="fn('aaa')">AAA</button>
        <button type="button" @click="fn('bbb')">BBB</button>
        <keep-alive>
            <component v-bind:is="view"></component>
        </keep-alive>
    </template>
</div>
<script>
var app = new Vue({
    el: '#app', data: { view:"bbb" },
    methods: { fn(str){this.view=str} },
    components: {
        "aaa": {template:"<div>aaa</div>", created(){console.log('aaa')}},
        "bbb": {template:"<div>bbb</div>", created(){console.log('bbb')}},
        "ccc": {template:"<div>ccc</div>", created(){console.log('ccc')}}
    }
});
</script>
```

点击app.vue中的按钮，切换组件，观察created执行了几次。



## 异步组件

```
<div id="app">
    <button type="button" @click="fn">加载组件</button>
    <component v-bind:is="view"></component> 	
</div>
<script>
var app = new Vue({
    el: '#app', data: { view:"" },
    methods: {
        fn(){
            // 如果通过webpack捆绑单文件组件，可以使用 require 引入 
            // this.view = function(resolve){ require(["./abc.vue"], resolve); }
            // 如果是 vue.min.js 这种 script 标签引入，可以使用 fetch 获取一段组件数据
            this.view = {template:"<div>这个对象应该由ajax之类的方法异步获取</div>"}
        }
    }
});
</script>
```

## 函数化组件


Vue.js 组件提供了一个 functional 开关，设置为 true 后，就可以让组件变为无状态、无实例的函数化组件。因为只是函数，所以渲染的开销相对来说，较小。

```html
<div id="app">
    <abc></abc>
</div>
<script>
    Vue.component('abc', {
        functional:true,    // 为无状态组件后，不能写template只能写render
        render: function (createElement, context) {
            // createElement() 的返回值就是一个VNode(虚拟节点)
            return createElement('div', {style:{color:'green'}}, '你好')
        },
        mounted(){
            // 无状态组件时，不会存在生命周期
            // 组件的生命周期，钩子函数
            console.log('页面渲染完毕了')
        }
    })

    var app = new Vue({
        el: '#app'
    })
</script>
```

使用 functional:true 后，组件内也无法接收 props 属性了。


## .native 将原生事件绑定到组件

父页面

```
<Hello @click.native="fn"></Hello>
```

如果没有.native，那么点击hello是无效的；有了.native，则表示在组件根元素上添加这个事件。

```
methods:{
  fn(event){
      console.log(event.currentTarget)
  }
}
```

hello 子页面

```
<div style="background:pink; padding:20px">          
    <input >
</div>
```

如果我们想把事件关联到其他元素上（非根元素）怎么办？

父页面

```
<Hello @click="fn"></Hello>
```

```
fn(event){
  console.log(event.currentTarget)
}
```

子页面

```
<div style="background:pink; padding:20px">          
  <input @click="fn" >
</div>
```

```
data() { return {} },
props: [],
created(){
    console.log(this.$listeners)  // 获取该组件上的所有事件及绑定函数
    this.fn = this.$listeners.click;
}
```


## 程序化的事件侦听器

```javascript
created(){
    //this.$once("aa", this.aaa)
    this.$on("aa", this.aaa)
},
methods:{
    aaa(){
        console.log(this.$parent.a++)
        console.log('aaa()被执行了')
    },
    fn(){
        this.$emit("aa")
    },
    fn2(){
        this.$off("aa", this.aaa)
    }
}
```


# EventBus事件总线


eventbus.js

```
import Vue from 'vue'
const eventbus = new Vue();
export default eventbus;
```

main.js

```
import eventbus from './eventbus.js'
Vue.prototype.$eventbus = eventbus
```

任意组件A（监听事件）

```
mounted(){ this.$eventbus.$on("fnName", function(payload){ }) }
```

任意组件B（触发事件）

```
this.$eventbus.$emit("fnName", {a:2})
```