# vuex 状态管理（重点）

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。

集中式状态管理，可预期的对状态进行改变。

> 状态就是数据。

## vuex 解决的是什么问题？

一个 vue 项目是由若干个组件组合而成的，复杂的组件关系中，数据如何来管理，以前用 eventbus 来解决，现在官方推出了 vuex ，我们用 vuex 来解决它。

## 安装 vuex

vuex 需要独立安装

```bash
npm install vuex -S
```

## 创建 store 对象

每一个 Vuex 应用的核心就是 store（仓库）

store 基本上就是一个容器

新建 store.js 页面，在里面写代码：

```javascript
import Vue from 'vue';
import Vuex from 'vuex';

Vue.use(Vuex);  // 让每一个组件都能够使用vuex

// 创建vuex实例
const store = new Vuex.Store({
    state : {
        goods : []
    }
})

// 导出模块
export default store;
```

## 将 store 注入到 vue 中

在 main.js 页面中引入 store

```javascript
import store from './store.js';

new Vue({
    store
})
```

## vuex 提供了一些属性

- state  用来存储状态的容器
- mutations  用来存储改变state的方法的容器
- actions  用来存储异步代码和业务逻辑代码，组件页用户触发的是这个函数
- getters  类似计算属性
- modules  状态特别多时，可以用modules将状态进行分类


## state

state 单一状态树：每个应用只能包含一个 store 实例。

state 就是用来存储状态的容器，状态就是数据。

store.js

```javascript
const store = new Vuex.Store({
    state: {
        count: 0
    }
});
```

abc.vue

```javascript
export default {
    data () {
        return { }
    },
    computed: {
        count(){ 
            // 把vuex中的count属性赋给vue组件中count属性
            return this.$store.state.count;
        }
    }
}
```

## mutations

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。

store.js

```javascript
const store = new Vuex.Store({
    state: {
        count: 1
    },	
    mutations: {
        // 定义方法，这个方法的作用是更新count的状态
        fn2(state){ state.count++; }
    }
})
```

abc.vue

```javascript
export default {
    computed: {
        count(){
            return this.$store.state.count;
        }
    },
    methods: {
        fn(){
            // 调用 vuex 中 mutations 中的方法
            this.$store.commit('fn2');
        }
    }
}
```

```html
<div @click="fn">{{count}}</div>
```

## actions

业务逻辑代码和异步任务代码不应该写在 mutations 中，而应该写在 actions 中，这样的划分，是代码更具有层次，便于后期维护。

```javascript
const store = new Vuex.Store({
    state : {
        count: 1
    },
    mutations : {
        fn2(state, payload){
            state.count++;
        }
    },
    actions:{
        fn1(context, payload){
            context.commit('fn2');
        }
    }
})
```

abc.vue

```javascript
export default {
    computed: {
        count(){
            return this.$store.state.count;
        }
    },
    methods: {
        fn(){
            // 调用 vuex 中 actions 中的方法
            this.$store.dispacth('fn1');
        }
    }
}
```

```html
<div @click="fn">{{count}}</div>
```

## getters

getters 就是 vuex 中的计算属性，它具有缓存的特点。

```javascript
const store = new Vuex.Store({
    getters:{
        allMoney( state ){
            return 123
        }
    }
})    
```

abc.vue

```javascript
export default {
    computed: {
        allMoney(){
            return this.$store.getters.allMoney;
        }
    }
}
```

## 辅助函数

- mapState  在vue组件页中，快速接收vuex中state中的数据。
- mapMutations  在vue组件页中，快速接收vuex中mutations中的数据。
- mapActions  在vue组件页中，快速接收vuex中actions中的数据。
- mapGetters  在vue组件页中，快速接收vuex中getters中的数据。

abc.vue

```javascript
import {mapState, mapMutations, mapActions, mapGetters} from 'vuex'

export default {
    computed: {
        ...mapState(['a']),
        ...mapGetters(['b'])
    },
    methods: {
        ...mapMutations(['c']),
        ...mapActions(['d'])
    }
}
```

在组件页面中，使用辅助函数接收到的vuex数据，可以直接使用。


## modules

一个项目中，代码特别多时，可以用模块将代码分割开。

store.js

```javascript
const store = new Vuex.Store({
    modules: {
        a : {
            state:...
            mutations:...
            actions:...
        }
    }
})
```

abc.vue

```javascript
this.$store.state.a
```

> 在vue组件页获取模块中的state，需要用到模块名称；在模块页mutations、actions中不需要用模块名称就可以直接获取当前模块的state。

state 需要通过模块名称查找，而mutations、actions、getters是不需要的

例如：this.$store.dispatch('abc')

如果多个模块中都存在abc这个事件，那么这几个事件都会被触发。


## namespaced

给模块设置命名空间后，调用mutations、actions、getters时，需要加上命名空间。

```javascript
const store = new Vuex.Store({
    modules:{
        a : {
            actions:{
                add(){
                    console.log('a')
                }
            }
        },
        b : {
            namespaced:true,
            actions:{
                add(){
                    console.log('b')
                }
            }
        },
        c : {
            actions:{
                add(){
                    console.log('c')
                }
            }
        }
    }    
})
```

```javascript
// 可以单独执行 b 模块下的 add 事件
this.$store.dispatch("b/add");

// 可以执行 a 和 c 模块下的 add 事件
this.$store.dispatch("add");
```

## 辅助方法及modules

```javascript
{
    methods:{
        ...mapActions({
            sub : 'g/sub',
            add : 'g/add'
        })
    },
    computed:{
        /*
        goods(){
            return this.$store.state.g.goods;
        },
        allMoney(){
            return this.$store.getters['g/allMoney'];
        }
        */
        ...mapState({
            goods: (state,getters)=>{
                return state.g.goods;
            }
        }),       
        ...mapGetters({
            allMoney: 'g/allMoney' 
        })       
    }
}
```

## subscribe

每次执行vuex中mutation函数后，会自动触发订阅事件。

store.js

```javascript
const store = new Vuex.Store({
    ...
})

store.subscribe((mutation, state)=>{
    // 每次执行完 mutation 后，该订阅函数会被自动触发
})
```




# directive 指令

全局：

```javascript
Vue.directive('focus', {
	inserted: function (el) {
        el.focus()
    }
})
```

局部：

```javascript
directives: {
	focus: {
        inserted: function (el) {
            el.focus()
        }
    }
}
```

使用：

```html
<input v-focus>
```

```javascript
data(){  return { y:1, s:true } },
template: `
    <div>
        <button @click="s=!s">移除指令</button>
        <input v-xyz:x="y" v-model="y" v-if="s">
    </div>
`,
directives: {
    xyz: {
        bind() {
            console.log(`bind只调用一次，指令第一次绑定到元素时调用。
            在这里可以进行一次性的初始化设置。`)
        },
        inserted() {
            console.log(`inserted被绑定元素插入父节点时调用 
            (仅保证父节点存在，但不一定已被插入文档中)。`)
        },
        update() {
            console.log(`update所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。
            指令的值可能发生了改变，也可能没有。
            但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。`)
        },
        componentUpdated() {
            console.log("componentUpdated指令所在组件的 VNode 及其子 VNode 全部更新后调用。")
        },
        unbind() {
            console.log("unbind只调用一次，指令与元素解绑时调用。")
        }
    }
}
```


# filter 过滤

过滤器的作用是，将数据渲染到页面上时，先将数据处理一下，把处理后的新数据渲染到页面上。

全局

```html
{{ 2 | r(3) }}
```

```javascript
Vue.filter('r', function (value, d) {
    return value*d
})
```

局部

```html
{{ 2 | r(3) }}
```

```javascript
new Vue({
    filters: {
        r(value, d) {
            return value*d
        }
    }
})
```


# plugin 插件

插件为 Vue 提供全局功能

定义插件    abc.js

```javascript
const plugin = {
    install(Vue, options){
        Vue.component(options.compName, {
            template:`
                <div>plugin ${options.num}</div>
            `
        })
    }
}
export default plugin
```

引入及使用   main.js

```javascript
import pp from './plugins/abc.js'
Vue.use(pp, {compName:"pppp", num:1})
```

因为定义插件时，注册的是全局组件，所以任意.vue页面中可以直接使用<pppp>

**toast**

index.js

```javascript
import Toast from "./Toast.vue";

const toast = {}

toast.install = function(Vue, options){
    const Comp = Vue.extend(Toast);
    const vm = new Comp().$mount();
    document.body.appendChild(vm.$el);

    Vue.prototype.$toast = function(text){
        vm.show = true;
        vm.txt = text;
        setTimeout(()=>{
            vm.show = false;
        }, 2000)
    }
}

export default toast;
```

Toast.vue

```html
<template>
    <transition
        enter-active-class="animated fadeIn"
        leave-active-class="animated fadeOut"
    >
        <div class="toast" v-if="show">{{txt}}</div>
    </transition>
</template>

<script>
export default {
    data(){
        return {
            txt : "",
            show : false
        }
    }
}
</script>

<style scoped>
.toast{
    position: fixed;
    left: 50%;
    top: 50%;
    transform: translate(-50%,-50%);
    background:rgba(0,0,0,.8);
    color: white;
    padding: 10px;
    border-radius: 5px;
}
</style>
```

main.js

```javascript
import toast from './plugins/toast/index.js'
Vue.use(toast)
```


# UI

vue 中有很多 UI 框架，业内比较常用的是 “饿了吗” 提供的两款框架，分别是移动端的 Mint-UI，和 PC 端的 Element-UI。

## Mint-UI

http://mint-ui.github.io/#!/zh-cn

下载

```bash
npm install mint-ui -S
```

使用

```javascript
import Mint from 'mint-ui'
import 'mint-ui/lib/style.css'

Vue.use(Mint)
```

```html
<mt-button>按钮</mt-button>
```

## Element-UI

http://element.eleme.io/#/zh-CN/component/installation

下载

```bash
npm i element-ui -S
```

使用

```javascript
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);	
```

```html
<el-button>Click Me</el-button>
```