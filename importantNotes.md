# 06
1. 什么是虚拟dom 什么是 diff 算法 ?
    - 虚拟dom也就是我们常说的虚拟节点,它是通过js的object对象模拟dom中的节点,然后再通过特定的render方法将其渲染成真实的dom节点
    - 为什么使用虚拟dom  --> 重绘 回流(重排)
    - diff 算法 ---> dom节点怎么更新

2. mixins:[options1,options2] 

3. 组件创建 
    -   Vue.component() --> 全局组件
    -   写在new Vue()内 --> 局部组件

4. 模块化开发 js    组件化开发 html css js(便于维护)  使用webpack

5. props 用来传递数据 

6. 单向数据流  父向子传递 --> 通过 props 传递数据 --- 面试

7. prop 验证 --- 基本用不到

8. 子页面向父页面传递数据 -- 面试
   
- this.emit('abc','hello');//执行自身上的abc事件 触发abc事件相当于调用abc关联的xyz函数,向xyz函数中传入参数'hello'
  
9. $parent $children $root   组件间的数据传递

10. provide inject  组件间的数据传递

11. eventbus 中央事件总线

12. slot 插槽 (重点) 作用: 将 html 结构传入组件内  具名插槽

13. 钩子函数 提前预设的行为,到了某个阶段可以自动触发某些事
    > 动画回调函数
    - v-on:before-enter="beforeEnter"
    - v-on:enter="enter"
    - v-on:after-enter="afterEnter"
    - v-on:enter-cancelled="enterCancelled"
    - v-on:leave-enter="beforeLeave"
    - v-on:leave="leave"
    - v-on:after-leave="afterLeave"
    - v-on:leave-cancelled="leaveCancelled"

    - beforeEnter 渲染之前
    - enter 渲染的过程执行
    - afterEnter 执行完成
    - enterCancelled  中途取消

    > 生命周期 钩子函数 **************重点
    > 钩子函数: 实例在不同阶段,自动触发的事件
    > Vue实例有一个完整的生命周期, 从开始创建,初始化数据,编译模板,挂载dom,渲染->更新->渲染,销毁 一系列过程, 通俗来讲就是从创建到销毁的整个过程
    > 生命周期分为三个阶段: 初始化, 运行中, 销毁
    > 1. beforeCreate 实例初始化之后  
    > created  实例创建完成立即调用
    > beforeMount 在挂载开始之前被调用(挂到html上)
    > mounted  挂载完成之后被调用
    > 2. beforeUpdate  数据更新时被调用 更新dom前
    >     updated  数据更新导致dom重新渲染后被调用
    > 3. beforeDestroy  实例销毁前被调用
    >    destroyed  实例销毁后被调用
14. vue-cli (重点) 用来管理vue项目的工具,可以用vue-cli 快速创建项目,启动项目,编译项目等操作
    - npm uninstall vue-cli -g
    - npm install @vue/cli -g


15. spa 单页面应用(路由) 优点: 响应快,请求次数少  不足: 第一次请求慢请求数据多

16. this.$route.params  路由动态参数 
    - <router-link to="/params/123">params</router-link>
    - router.js 文件 --> { path:'/params/:id',component:组件名,created(){ console.log(this.$route.params) } }

17. this.$route.query
    - <router-link to="/query?a=1&b=2">query</router-link>
    - router.js 文件 --> { path:'/query',component:组件名,created(){ console.log(this.$route.query) } }

18. 声明式导航 <router-link to="/path">跳转到to里面的链接<router-link>
    编程式导航 this.$router.push('/path');  this.$router.push({path:'/path',query:{a:1,b:2}})  
             this.$router.push('/path?a=1&b=2'); this.$router.push('/path/1/2');

19. 声明式导航和编程式导航都会将记录添加到 history 中
    不将记录添加到 history 中 
    - <router-link to="/path" replace>添加replace<router-link/>
    - this.$router.replace('/path/query?a=1&b=2') 

20. 路由对象下有两个常用的属性: $router 和 $route
    - $route 里面存放的是属性: $route.query  $route.params
    - $router 里面存放的是方法: $router.go() $router.replace() $router.push()
        > $router.go(1)  1:前进一页   -1:后退一页    0:刷新  
        > $router.replace('/path')  不在 history 中记录历史
        > $router.push('路径')  编程式导航--通过js的方式跳转

21. 守卫 -- 实际上就是钩子函数 -- 当页面发生跳转的时候,会被执行 
    - 全局守卫 : 项目中任何页面发生跳转之前, 都会自动触发的钩子函数 --写在router.js里
      
        > router.beforeEach((to,from,next)=>{ console.log(to,from); next(); })
    - 路由守卫 : 写在路由规则里,只监视当前的组件
    
        > router.beforeEnter((to,from,next)=>{ console.log(to,from); next(); })
    
22. 移动端滚动条效果, npm i iscroll -S

23. 类似于juery 的 常用在移动端的 zepto  bootcdn 引入

24. Vuex 状态管理模式  状态-->数据
    - 产生背景 
        > 组件化开发:让代码结构更清晰,便于维护
        > 组件:封装,复用
    - 解决什么问题:
        > 数据存储和维护的问题
        > 集中式管理状态(数据),及可预期式对状态(数据)进行改变
    - npm i vuex
    - import Vuex from "vuex"
    - Vue.use(Vuex)
    
25. vuex 中 mutations 和 actions 有什么区别? ---面试
    - mutations 是最后一步用来改变state数据的
    - 在actions 里处理业务逻辑和异步操作, 这样看起来更简洁明了 

26. mapState 辅助函数  通过辅助函数快速获取state  import {mapState} from 'vuex'

27. modules  把不同功能的模块分开, 注意: 在获取state数据的时候必须加上modules里面的模块名

28. directive 自定义指令

29. filter 过滤器

30. plugin 插件

31. swiper 轮播图 使用方式: cdn / npm i swiper -S

32. mint ui 移动端

33. element ui

34. SSR 服务器渲染解决什么问题?

    - 访问一个网站右键检查源代码不会显示,搜索引擎无法识别网站有什么东西,对搜索引擎不友好

35. FileZilla
















​    

## 服务的渲染(SSR) 与 Nuxt 框架
1. npm install create-nuxt-app -g
方式一:
    - npx create-nuxt-app <项目名>
    - cd <项目名>
    - npm install 

    首屏渲染 -- node 渲染
    路由跳转 -- 客户端渲染
    process.server 判断是服务端还是客户端


# Vue 知识梳理
1. Vue MVVM 数据模型    M:model(数据data)  V:view(视图层)  VM:new Vue()实例

2. vue实现数据绑定的原理: Object.defineProperty(obj,'属性名称',{ get(){  }, set(){  } }) 获取数据时(let num = obj.a)自动执行get()方法,设置数据时(obj.a=10)自动执行set()方法

3. 实际上我们可以创建多个vue app 实例, 只要能保证vue实例接管不同的div也行

4. v-if 和 v-show 区别, 优缺点

5. v-for 遍历输出, 绑定 key 值, 每一个li返回一个唯一的值, 有key值,在渲染时可以提高性能, 复用数据(在缓存中寻找key值,速度更快,性能更好)

6. @click="fn" --- fn 和 fn($event)  的区别?
   
- fn()  可以自己传参数,更灵活
  
7. v-model 用在text,password,textarea,checkbox,select,
    - <input v-model="show" type="checkbox">  show为true自动打勾,false不打勾
    - <input v-model="lessons" type="checkbox" v-bind:value="lesson"> lessons为数组,lesson为数组项 ,数组项的值在数组中打勾, 点击时,会将 lesson push 到 lessons 中, 再次点击,从数组中删除该项
    - <input v-model="sex" name="sex" type="radio" value="男"> 当 v-model 绑定的变量和当前 value 的值相同,选择, 点击时,会将所点击input的 value 值赋给 v-model

8. 组件: 更利于维护 根据功能将页面划分成不同的组件 -- 组件可以复用,提高开发效率

9. - 生命周期: -- 面试 beforeCreate() created() beforeMount() mounted() 初始化 beforeUpdate() updated() 运行中 beforeDestroy() destroyed()  销毁
   - 钩子函数: 实例在不同阶段自动触发的函数

10. - 插值表达式 {{}}
    - v-once  只会执行一次
    - v-html  可以解析html标签
    - v-text  解析成文本
    - v-on    绑定事件 @ v-on:[eventName]="donSomething"
    - v-bind  绑定属性 : 
    - v-if    v-else    v-else-if    v-for

11. computed 计算属性  -- 利用缓存,提高性能

12. 计算属性 (利用缓存,只有在数据发生改变时才重新渲染) VS 方法 (多次调用,多次渲染)

13. 计算属性 VS watch (可以用watch监听数据模型,数据改变利用回调函数处理一些业务逻辑)

14. class 和 style    ***********
    - :class="{active:isActive}"
    - :class="[]"

    - :style="{color:activeColor,fontSize:fontSize+'px'}"

15. <template> 临时标签,不会渲染到页面上

16. v-show (display) 和 v-if (创建删除)  区别

17. v-if 与 v-for 不推荐同时使用 解决: <template> 包一下

18. 数组更新检测
    - 变异方法 -- 改变原数组
    - 替换数组 -- concat()  slice()  filter()

19. 组件的复用: 组件的data必须是函数 return 一个对象

20. props 父组件向子组件传递数据

21. 动态组件 -- <component :is="动态创建的组件"></component>

22. <keep-alive></keep-alive> 缓存动态组件














