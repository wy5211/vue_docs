# vue 是什么？

Vue.js是一套构建用户界面的渐进式框架。

- Vue.js 是一款极简的 mvvm 框架（MVVM实现核心：Object.defineProperty）
- Vue不支持IE8及以下版本
- 尤雨溪是Vue.js框架的作者，2014年2月，开源了一个前端开发库Vue.js。
- 2016年9月3日，Vue作者尤雨溪正式宣布以技术顾问的身份加盟阿里巴巴Weex团队。

Vue 常见的两种使用方法：

- 1，直接通过 script 标签引入 vue 文件。
```html
<script src="https://cdn.bootcss.com/vue/2.5.21/vue.min.js"></script>
```
- 2，通过 webpack 引入 vue 文件。


# 模版语法

数据驱动、编程式渲染：当改变数据时，视图会自动更新。

插值表达式 Mustache 语法(双大括号)。

```html
<div id="app">
    {{ message }}
</div>
<script>
var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello Vue!'
    }
})
</script>
```

```
MVVM 把代码分了三层
M 模型层 （就是用来保存数据）
V 视图层 （显示html的内容，渲染到页面上的内容）
VM 视图模型层 （vue这个框架）用来连接M与V这两层
```

```
数据的双向绑定
当M层发生变化，通过vm层，让v层自动更新
当V层发生变化，通过vm层，让m层自动更新
```

支持表达式，但不支持语句。

```html
{{ number + 1 }}
{{ ok ? 'YES' : 'NO' }}
{{ message.split('').reverse().join('') }}
<div v-bind:id="'list-' + id"></div>
```

# v-bind 绑定属性

v-bind可以把div中的title属性与vue中message属性绑定在一起。
	
```html
<div v-bind:title="message">hello</div>
v-bind 可以简写
<div :title="message">hello</div>

<script>
var app = new Vue({
    el: '#app',
    data: {
        message: 'Hello Vue!'
    }
})
</script>
```

# v-once、v-html、v-text

把模型层的数据，渲染到视图层的指令。

```html
<div id="app">
    <span v-once>{{a}}</span>
    <span v-html="a"></span>
    <span v-text="a"></span>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            a:'<font color=red>hello</font>'
        }
    }
})

// 两秒后修改数据，发现v-once内容不会变化。
setTimeout(()=>{
    vm.a = "hello";
}, 2000)
</script>
```

# methods方法与v-on指令（重点）

v-on 绑定事件

```html
<div id="app">
    <input type="botton" value="按钮" v-on:click="fn"  />
</div>
<script>	
var app = new Vue({    
    el: '#app',
    data: { a:1, b:2 },
    methods: { 
        fn(){
            alert( this.a + this.b );
        }
    }
}})    
</script>
```

简写

```html
<div id="app">
    <div>{{a}}</div>
    <button @click="handle">按钮</button>
</div>
<script>
var app = new Vue({
    el: '#app',
    data: {a:1},
    methods: { handle: function(){this.a++;} }
})
</script>
```

# class

```html
<div id="app">
    <div v-bind:class="abc"></div>
</div>
<script>	
var app = new Vue({    
    el: '#app',
    data: { abc:"tmp" }
}})    
</script>
```

```html
<div id="app">
    <div v-bind:class="classObj"></div>
</div>
<script>	
var app = new Vue({    
    el: '#app',
    data: { 
        classObj:{ "a":true, "b":false } 
    }
}})    
</script>
```

```html
<div :class="[a,b]">123</div> 
data: {	a : "x",	b : "y" } 

<div :class="classArr">123</div>	 
data: {	classArr : ["a", "b-c"] }
```

# style

```html
<div :style="{background:a,'font-size':c+'px'}">123</div> 
data: { a:'blue', c:100 } 

<div :style="classObj">123</div> 	 
data: { classObj: { background:'yellow', 'font-size':'50px'} }

<div :style="[a, b]">123</div>
data: { a:{background:'yellow'}, b:{color:'orange'} }

<div :style="classArr">123</div>
data: { classArr: [{background:'red'}, {color:'green'}] }
```

# 条件渲染

## v-if

条件 v-if 指令：	

```html
<p v-if="flag">x</p>
<p v-else-if="flag===3">y</p>
<p v-else>z</p>
```

vue采用就地复用原则：Vue 会尽可能高效地渲染元素，通常会复用已有元素而不是从头开始渲染。（子节点就地复用）

如果不想采用就地复用，请使用key

```html
<div id="app">
    <input type="button" value="toggle" @click="handle" />
    <div v-if="bln">
        用户名：<input key="a" />
    </div>
    <div v-else>
        邮箱：<input key="b" />
    </div>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            bln : true
        }
    },
    methods:{
        handle(){
            this.bln = !this.bln;
        }
    }
})
</script>
```

template把多个标签打包，渲染时直接渲染里面的节点。

```html
<div id="app">
    <input type="button" value="toggle" @click="handle" />
    <template v-if="bln">
        <h1>标题</h1>
        <p>内容</p>
    </template>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            bln : true
        }
    },
    methods:{
        handle(){
            this.bln = !this.bln;
        }
    }
})
</script>
```

## v-show

条件 v-show 指令：

```html	
<li v-show="bln">你能看到我吗？</li>
```

## v-if vs v-show


v-show 值为false时，仅把display改为none     
        不支持template，也不支持 else

v-if 值为false时，把dom元素删掉了

```
v-if 是“真正”的条件渲染，因为它会确保在切换过程中条件块内的事件监听器和子组件适当地被销毁和重建。

v-if 也是惰性的：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，v-show 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，v-if 有更高的切换开销，而 v-show 有更高的初始渲染开销。

因此，如果需要非常频繁地切换，则使用 v-show 较好；如果在运行时条件很少改变，则使用 v-if 较好。
```

# 列表渲染v-for

## v-for

```html
<div id="app">
    <input type="text" v-model="str" />
    <input type="button" @click="fn" />
    <li v-for="(val, ind) in arr">
        {{val + ' ------- ' + ind }}
        <input type="button" value="删除" :data-ind="ind" @click="del($event)" />
    </li>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            str : "",
            arr : ["a", "b", "c", "d"]
        }
    },
    methods:{
        fn(){
            this.arr.push( this.str );
        },
        del(event){
            var ind = event.target.dataset.ind;
            console.log(ind);
            this.arr.splice(ind, 1)
        }
    }
})
</script>
```

## key

key：不使用key时，选中a、c、e三项，然后删除a，然后发现b、d两项被选中了。

所以使用for时，要写key。

```
<div id="app">
    <li v-for="(val, ind) in arr" :key="val.id">
        <input type="checkbox" />   
        {{ val.text  }}
        <input type="button" value="删除" :data-ind="ind" @click="del($event)" />
    </li>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            str : "",
            arr : [
                {"text":"a", "id":1},
                {"text":"b", "id":2},
                {"text":"c", "id":3},
                {"text":"d", "id":4},
                {"text":"e", "id":5},
            ]
        }
    },
    methods:{
        del(event){
            var ind = event.target.dataset.ind;
            this.arr.splice(ind, 1)
        }
    }
})
</script>
```

## 变异方法与非变异方法

变异方法：数据改变之后，视图会自动更新。

例如：push(), pop(), shift(), unshift(), splice(), sort(), reverse()，这些方法会改变原始数组。

非变异方法：数据改变之后，视图不会自动更新。

例如：filter(), concat() 和 slice() 。这些不会改变原始数组，但总是返回一个新数组。

```html
<div id="app">
    <li v-for="(val, ind) in arr" :key="ind">          
        {{ val  }}
    </li>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            str : "",
            arr : [
                {text:"1"}
                ,2,3,4,5,6,7
            ]
        }
    },
    methods:{
        fn(){
            var a = this.arr.filter(val=>{
                return val%2==0
            });
            console.log( this.arr )
            console.log( a )
            this.arr = a;
        },
        fn2(){
            //this.arr.length = 2  // 数据能够被删除，但是视图层不会自动更新
            //this.arr[0] = "abc" // 数据能够被删除，但是视图层不会自动更新
            this.arr[0].text = "abc" // 视图层会自动更新的
        }
    }
});
</script>
```



## $set更新

```html
<div id="app">
    <!--<li v-for="(val, ind) of arr" :key="ind">          
    {{ val+" --- "+ ind  }}
    </li>-->
    <li v-for="(val, key, ind) of obj" :key="ind">          
        {{ val+" --- "+ key+" --- "+ ind  }}
    </li>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            arr : [
                "a", "b", "c"
            ],
            obj : {
                "a" : "apple",
                "b" : "blue",
                "c" : "color"
            }
        }
    },
    methods:{
        fn(){
            //this.obj.d = "drag";    // 数据能够添加上，但是视图层不会更新               
            //this.$set(this.obj, "d", "drag");  // 操作对象

            //this.arr[1] = "blue";
            //this.$set(this.arr, 1, "blue")  // 操作数组
        }
    }
})
</script>
```

## for与if不写在一起

不推荐同时使用 v-if 和 v-for, eslint会包警告的

> eslint 是用来检查代码是否规范的机制，可在webpack中配置。

当 v-if 与 v-for 一起使用时，v-for 具有比 v-if 更高的优先级。

```
<li v-for="item in arr" v-if="item.show">
    {{ item.text }}
</li>
```

```html
<div id="app">
    <li v-for="item in arr2(arr)">
        {{ item.text }}
    </li>
</div>

<script>
var vm = new Vue({
    el:"#app",
    data(){
        return {
            arr : [
                {text:"a", show:true},
                {text:"b", show:false},
                {text:"c", show:true},
                {text:"d", show:true},
                {text:"e", show:false}
            ]
        }
    },
    computed:{
        arr2(){
            return function(arr){
                return arr.filter(item=>item.show)
            }
        }
    }
})

// 执行下面的代码，会把b显示到页面上行。
// vm.arr[1].show = true
</script>
```

## 一段取值范围的 v-for

输出的内容为 1 2 3 4 5
```
<li v-for="n in 5" :key="n">
    {{ n }}
</li> 
```

# v-clock

浏览器打开页面时，先渲染模版的原始内容，然后vue会自动对模版内的原始内容进行编译，生成新内容，然后把新内容渲染到页面上。

v-clock的作用就是vue对模版内容编译之前，先给该元素添加个css样式，等到编译完成后，再把样式取消掉。

把下面代码中css部分注释掉，然后使用火狐浏览器访问，会现在页面上看到{{msg}}，这是没有编译前的内容，alert结束后，{{msg}}就被替换了。

```html
<style>
    [v-clock]{
        display: none
    }
</style>
<div id="app">
    1
    <p v-clock>{{msg}}</p>
    3
</div>
<script>
var app = new Vue({
    el : '#app',
    data(){
        return {
            msg : '这是一段内容'
        }
    },
    beforeMount:function(){
        alert("确定执行")
    }
})
</script>
```

# 事件

## 传递参数

```html
<div id="app">
    <button @click="sum(1,2)">按钮</button>
</div>
<script>
var app = new Vue({
    el: '#app',
    methods: {sum(a, b){alert(a+b)} }
})
</script>
```

## event

```html
<div id="app">
    <button @click="handle(123,$event)">按钮</button>
</div>
<script>
var app = new Vue({
    el: '#app',
    methods: { handle(num, event){console.log(event);} }
})
</script>
```

# 事件修饰符

## stop阻止冒泡

```html
<div id="app">
    <div @click="fn1">div1
        <div @click.stop="fn2">div2</div>
    </div>
</div>
<script>
var app = new Vue({
    el: '#app',
    methods: { 
        fn1(e){ console.log('div1');},
        fn2(e){ console.log('div2');}
    }
})
</script>
```

## 事件修饰符可以串联在一起

```html
<div id="app">
    <div @click="fn1" id="div1">div1
        <form action="http://www.baidu.com">
            <button type="submit" @click.stop.prevent="fn2">提交</button>
        </form>
    </div>
</div>
<script>
var app = new Vue({
    el: '#app',
    methods: { 
        fn1(e){ alert('div1');},
        fn2(e){ alert('div2');}
    }
})
</script>
```

## 使用修饰符时，顺序很重要

```html
<ul @mousedown.prevent="fn"> aaa  
    <li>bbb</li>   
</ul>
```

```javascript
methods: {
    fn(){console.log(123)}
}
```

> @mousedown.prevent="fn"
>> ul 和 li 被点击时，fn都会执行，文字都不会被选中。

> @mousedown.prevent.self="fn"
>> 语义：先阻止默认行为，然后给自身绑定fn
>> ul的fn会执行，li的fn不会执行，文字都不会被选中。

> @mousedown.self.prevent="fn"   
>> 语义：先给自身绑定fn，然后阻止默认行为
>> ul的fn会执行，ul的文字不会被选中；li的fn不会执行，li的文字可以被选中

## capture

设为捕获

```html
<div id="app">
    <div @click.capture="fn1">
        div1
        <div @click.capture="fn2">
            div2
            <div @click.capture="fn3">
                div3
            </div>
        </div>
    </div>
</div>
<script>
    var app = new Vue({
        el: '#app',
        methods: {
            fn1() { console.log(1) },
            fn2() { console.log(2) },
            fn3() { console.log(3) }
        }
    })
</script>
```

## once

仅执行1次

```html
<div id="app">
    <div @click.once="fn1">
        div1
    </div>
</div>
<script>
    var app = new Vue({
        el: '#app',
        methods: {
            fn1() { console.log(1) }
        }
    })
</script>
```

## self

仅在自身触发

```html
<ul @mousedown.self="fn"> aaa  
    <li>bbb</li>   
</ul>
```

点击ul时，会触发fn；点击li时，不会触发fn

## 键盘修饰符

在输入框中，按下会车后，执行fn。（回车的键盘码是13）

```
<input @keyup.13="fn" />
```

可以自己设置键盘按键的修饰符

```
<input @keyup.a="fn" />
<script>
Vue.config.keyCodes.a = 65;
</script>
```

系统自带的修饰符

```
enter
tab
delete
esc
space
up
down
left
right
```

组合键

```
<input @keyup.ctrl.13="doSomething">

<div @click.shift.alt="doSomething"> // 不要用ctrl
```

## 鼠标修饰符

```
<div @click.right.prevent="fn">自定义右键</div>
left
middle
right
```

## .passive

```javascript
window.addEventListener("scroll", function(e){
    e.preventDefault()
    console.log( document.documentElement.scrollTop )
}, {passive:true}) //passive:false时，阻止默认事件，ture时不阻止默认事件(默认值是false)
// true 表示不允许浏览器调用 preventDefault事件，如果调用了，会报警告。
// 优化的一种方案，即用户操作时，程序这边不知道函数中有没有preventDefault，
// 必须等待一定的时间才能知道函数中到底有没有preventDefault，
// 所以注册事件时，直接说这个函数中没有preventDefault
// {capture:false, once:false, passive:false} 默认值都是false
```

```html
<div @scroll.passive="fn">    
    <ul></ul>
</div>
```

```css
div{
    height: 400px;
    overflow: auto;
    background: pink;
}
ul{
    height: 4000px;
}
```

```javascript
methods: {
    fn(e){
        console.log(123);
        // e.preventDefault() // 这句和passive不能同时出现
    }
}
```

## .exact

.exact 修饰符允许你控制由精确的系统修饰符组合触发的事件。

> 不要用click，因为 click与ctrl合用后出现选中

```html
<!-- 即使 Alt 或 Shift 被一同按下时也会触发 -->
<button @mousedown.ctrl="onClick">A</button>

<!-- 有且只有 Ctrl 被按下的时候才触发 -->
<button @mousedown.ctrl.exact="onCtrlClick">A</button>

<!-- 没有任何系统修饰符被按下的时候才触发 -->
<button @mousedown.exact="onClick">A</button>
```

```javascript
onClick(e){
    console.log('onClick');
},
onCtrlClick(e){
    console.log('onCtrlClick');
}
```


# v-model 表单输入绑定

v-model 数据双向绑定指令

```
<textarea v-model="a"></textarea>
{a : 123 }


{{cb}}<input type="checkbox" v-model="cb" value="吃" />
{cb : true}


{{cb}}<input type="checkbox" v-model="cb" true-value="yes" false-value="no" />
{cb : 'yes' }


{{cb}}<input type="checkbox" v-model="cb" value="吃" />
<input type="checkbox" v-model="cb" value="喝" />
{cb : ['玩'] }


{{ra}} <input type="radio" v-model="ra" value="吃" />
<input type="radio" v-model="ra" value="喝" />
{ra : '喝' }


{{se}} <select v-model="se">
<option value="1">张三</option>
<option value="2">李四</option>
</select>
{se : '2' }


{{se}} <select v-model="se" multiple>
<option value="1">张三</option>
<option value="2">李四</option>
</select>
{se : ['2']}

```

v-model 支持修饰符

## .lazy

lazy 懒惰的；如果没有lazy，则input时触发；有lazy，改为change时触发。

```html
<input type="text" v-model.lazy="a" /><br>
{{a}}
```

## .number

如果想自动将用户的输入值转为数值类型，可以给 v-model 添加 number 修饰符

```
<input type="text" v-model.number="a" />
```
这通常很有用，因为即使在 type="number" 时，HTML 输入元素的值也总会返回字符串。如果这个值无法被 parseFloat() 解析，则会返回原始的值。

## .trim

如果要自动过滤用户输入的首尾空白字符，可以给 v-model 添加 trim 修饰符

```
<input v-model.trim="msg">
```

# 作业题

todolist