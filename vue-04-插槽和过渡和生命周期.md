# slot 插槽（重点）

<slot> 元素作为承载分发内容的出口。

简单的说，就是向组件内部传入数据，在组件内部，使用slot标签表示传入到组件中的数据。

> 普通的数据用prop传递，html结构的这种数据用slot传递。

**app.vue**

```html
<Hello href="http://www.baidu.com"><i>百度</i></Hello>
```

解读：在app.vue中，使用Hello组件，向Hello组件传入<i>百度</i>数据，这个数据slot数据。

**hello.vue**

```html
<a :href="href">     
    <slot></slot>
</a>
```

## slot 编译作用域

变量 a 需要在 Hello 组件外部定义。

```
<Hello href="http://www.baidu.com"><i>{{a}}</i></Hello>

data () {
    return { a:"百度" }
}
```

父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。

## slot 后备内容 （默认值）

**app.vue**

```html
<Hello href="http://www.baidu.com"></Hello>
```

如果使用 Hello 组件时，没有向组件内传入数据，那么组件内部的 slot 的默认值将起作用。

**hello.vue**

```html
<a :href="href">     
    <slot>默认值</slot>
</a>
```

## slot 具名插槽

**app.vue**

```
<Hello>
    <template v-slot:title>标题</template>
    内容
    <template v-slot:footer>脚底</template>
</Hello>
```

**hello.vue**

```
<slot name="title"></slot>
<slot></slot>
<slot name="footer"></slot>
```

> v-slot 只能添加在 template 上

> slot标签没有name属性时，解析的是 v-slot:default

> slot内容如果没有用template和v-slot指定名称，那么属于default


## 插槽 prop

作用域插槽 --- 插槽 prop （里向外传递数据）

**app.vue**

```
<Hello>
    <template v-slot:title>标题</template>
    <template v-slot:footer>脚底</template>
    <template v-slot:default="slotProps">{{slotProps.a.y}}</template>
</Hello>
```

解读：在 app.vue 中渲染 hello 组件，向 hello 组件中传入3段内容。
* 重点看第3段内容，即 slot 名为 default 这个，我们做的事情是，hello 组件内提前定义一些数据，然后希望在外面能够拿到组件内的数据使用，default="slotProps"的作用就是定义了作用域插槽，即把hello组件中定义的数据拿出来，在这个template中使用。这种特性被称为“插槽 prop”
* slotProps 获取的是组件传递过来的所有属性。
* <template v-slot:default="slotProps"></template> 如果 template 的内容是空的，那就走默认值，即后备内容。

**hello.vue**

```
<div>     
<slot name="title">后备内容标题</slot>
<slot name="default" v-bind:a="a">{{a.x+a.y}}</slot>
<slot name="footer">后备内容脚</slot>
</div>

data() {
    return {
        a:{
            x:1,
            y:2
        }
    }
}
```

## 独占默认插槽

独占插槽的前提是，只能有一个slot，如果有别的具名插槽，会报错。

**app.vue**

```
<Hello v-slot="slotProps">
    {{slotProps.a.y}}
</Hello>
```

**hello.vue**

```
<div>
    <slot v-bind:a="a">{{a.x+a.y}}</slot>
</div>
```

## 解构插槽 Prop

es6 解构赋值的写法

```
<Hello v-slot="{a}">
    {{a.y}}
</Hello>
```

解构赋值 改名 var {x:b} = {x:1} // b == 1

```
<Hello v-slot="{a:b}">
    {{b.y}}
</Hello>
```


## 动态插槽名

**app.vue**

```
<Hello>
  <template v-slot:[abc]="slotProps">
      任意 {{slotProps.a.y}}
  </template>
</Hello>

data () {
    return {
        abc:"todo"  
    }
}
```


**hello.vue**

```
<slot name="todo" :a="a">默认值</slot>

data() {
    return {
        a:{
            x:1,
            y:2
        }
    }
}
```

## 具名插槽的缩写

跟 v-on 和 v-bind 一样，v-slot 也有缩写，即把参数之前的所有内容 (v-slot:) 替换为字符 #。例如 v-slot:header 可以被重写为 #header：

```
<template v-slot:header>
    <h1>Here might be a page title</h1>
</template>
<!--改为-->
<template #header>
    <h1>Here might be a page title</h1>
</template>
```

> 该缩写只在其有参数的时候才可用。

```
<template #="slotProps">
改为：
<template #default="slotProps">
```

组件页

```
<slot name="default" :a="a">默认值</slot>
```

# 动画

## transition 过渡

Vue 在插入、更新或者移除 DOM 时，提供多种不同方式的应用过渡效果。

transition 是 vue 提供的组件，这个组件的作用是给其子节点添加动画效果。

```
<style>
    @import url(https://cdn.bootcss.com/animate.css/3.7.0/animate.min.css);
    
    .toast{ 
        transform: translate(-50%,-50%); 
        position: fixed; 
        left: 50%;
        top: 50%; 
        background:black;
        color:white;
    }
</style>
<div id="app">
    <button @click="fn">动画按钮</button>
    <transition
        enter-class=""
        enter-active-class="animated fadeIn"
        enter-to-class=""
        leave-class=""
        leave-active-class="animated fadeOut"
        leave-to-class=""
    >
        <div class="toast" v-if="isShow">Toast</div>
    </transition>
</div>
<script>        
    var app = new Vue({
        el: '#app',           
        data:{
            isShow:true,                
        },
        methods:{
            fn(){
                this.isShow = !this.isShow;
            }
        }
    })
</script>
```

解读：当toast对应的div被创建到页面上时，transition会给这个div添加enter相关的css样式，当toast这个div从页面上被删除掉的时候，transition会给这个div添加leave相关的css样式，所以transition组件就是做动画设置的组件。

## transition-group

transition只能有0个或1个子节点，而transition-group可以有零个或多个子节点。

循环渲染时，必须写key，并且key的值是唯一标识符（不要用数组下标）。

```
<div id="app">
    <input type="text" v-model="str" />
    <button @click="fn2">添加</button>
    <transition-group
        enter-class=""
        enter-active-class="animated fadeIn"
        enter-to-class=""
        leave-class=""
        leave-active-class="animated fadeOut"
        leave-to-class=""
    >
        <li v-for="(item) in arr" :key="item.id">
            {{item.val}}
            <input type="button" @click="fn3(item.id)" value="del" />
        </li>
    </transition-group>
</div>
<script>        
    var app = new Vue({
        el: '#app',           
        data:{
            str : "",
            arr : []               
        },
        methods:{
            fn2(){
                this.arr.push({"val":this.str, "id":new Date().getTime()});
            },
            fn3( id ){
                var ind = this.arr.findIndex(obj=>obj.id===id);
                this.arr.splice(ind, 1)
            }
        }
    })
</script>
```


## 钩子函数

动画回调函数（钩子函数）

```
<transition
    v-on:before-enter="beforeEnter"
    v-on:enter="enter"
    v-on:after-enter="afterEnter"
    v-on:enter-cancelled="enterCancelled"
    v-on:before-leave="beforeLeave"
    v-on:leave="leave"
    v-on:after-leave="afterLeave"
    v-on:leave-cancelled="leaveCancelled"
>

methods: {
    beforeEnter: function (el) {
        console.log('进入前', el)
    },
    enter: function (el, done) {
        console.log('进入...', el)
        done() // 表示完成动画
    },
    afterEnter: function(el){
        console.log('进入完成', el)
    },
    enterCancelled: function(el){
        console.log('取消进入', el)
    }
}    
```



# 生命周期 钩子函数（重点）

Vue实例有一个完整的生命周期，也就是从开始创建、初始化数据、编译模板、挂载Dom、渲染→更新→渲染、销毁等一系列过程，我们称这是Vue的生命周期。通俗说就是Vue实例从创建到销毁的过程，就是生命周期。

每一个组件或者实例都会经历一个完整的生命周期，总共分为三个阶段：初始化、运行中、销毁。

钩子函数：实例在不同阶段，自动触发的事件。

```	
beforeCreate(){console.log("beforeCreate实例初始化之后，数据观测和事件配置还未完成时")},
created(){console.log("created实例创建完成后立即调用")},
beforeMount(){console.log("beforeMount在挂载开始之前被调用（挂到html上）")},
mounted(){console.log("mounted挂载完成后被调用")},

beforeUpdate(){console.log("beforeUpdate数据更新时被调用（更新DOM前）")},
updated(){console.log("updated数据更新导致DOM重新渲染后被调用")},

beforeDestroy(){console.log("beforeDestroy实例销毁前被调用")},
destroyed(){console.log("destroyed实例销毁后被调用")}
```

> 用的比较多的是 mounted、created、updated
