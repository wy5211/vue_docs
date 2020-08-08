#  服务器端渲染

服务端渲染（SSR）与客户端渲染（BSR）都是数据渲染的主要的方式

**客户端渲染**

前端利用ajax等数据交互手段获取服务端提供的数据之后，渲染到HTML页面。

**服务器端渲染**

在响应内容中描述一个完整的HTML页面。

**从用户体验的角度讲，客户端渲染更好，用seo的角度讲，服务器渲染更好。**

# Nuxt.js （重点）

Nuxt.js 是基于 vue 的服务器端渲染框架。

安装 create-nuxt-app 工具

```
cnpm install -g create-nuxt-app
```

使用 create-nuxt-app 创建 nuxtjs 项目

```
create-nuxt-app myapp1
```

其他创建 nuxtjs 项目的方法：

```bash
# 运行 create-nuxt-app
npx create-nuxt-app myapp1

# 或者
yarn create nuxt-app myapp1
```

创建 nuxt 项目的过程中，会问我们选择哪种渲染方式，这里一定要选择 Universal（通用的、普遍的）。

> spa 是单页面应用，这种模式下，文件不会 ssr 渲染，所以 nuxt 就没有意义了。

```javascript
// 可以修改 nuxt.config.js 中的mode属性来看看具体渲染效果。
mode: 'Universal'   // Universal 可以ssr； spa 不会ssr
```

nuxt 项目创建完毕后，先进入到项目中，然后运行起来:

```bash
cd myapp1
npm run dev
```

应用现在运行在 http://localhost:3000 上运行。


# 修改服务端口

package.json

```
"config": {
    "nuxt": {
        "host": "0.0.0.0",
        "port": "3333"
    }
},
"script":{
    
}
```

# scss

nuxt 本身不直接支持 scss，需要先安装模块：

```bash
cnpm i node-sass sass-loader -D
```

```html
<style lang="scss" scoped>
</style>
```

# rem文件引入

```javascript
// 不要使用这种方式引入rem，因为刷新页面时，会报找不到document错误，这是ssr渲染造成的问题。
import '~/assets/js/rem.js'
```

> import 这种导入的作用是把碎片化的文件合并到一起。

在 static 目录下，建立 js/rem.js 文件。

> static 目录是存放独立的文件的。

我们应该使用 script 标签引入 rem.js 文件，但 nuxtjs 中没有 html 页面，需要写在 nuxt.config.js 中。

在 nuxt.config.js 文件中：

```javascript
head: {
    script:[
        { src: '/js/rem.js', async: true, defer: true }
    ]
},
```

重启网站服务后，就能够正常访问网站了。

> 刷新的时候，如果字体特别大，可以在 layouts/default.vue 中给 div{ font-size:0.3rem }


# 主布局页

layouts/default.vue 这个页面是主布局页面（入口页）。

<nuxt /> 可以理解成出口，每次触发路由跳转时，这部分内容会自动更新。



# 路由

在 pages 文件夹下创建 buycar/index.vue 文件

在任意 vue 页面中写链接

```html
<nuxt-link to="/mine">我的</nuxt-link>
```

当点击链接后，视图层会自动更新。



# 获取远程数据

下载

```bash
cnpm i axios -S
```

引入

```javascript
import axios from 'axios';
```

使用

> asyncData方法会在组件（限于页面组件）每次加载之前被调用。它可以在服务端或路由更新之前被调用。

```javascript
export default {
    async asyncData({ params }) {
        let { data } = await axios.get('/1.json');
        console.log('data:', data);
        return data;    // 这个return会把结果和data属性的值自动合并，视图层直接调用即可。
    }
};
```

> 在 static 中新建 1.json 做模拟数据，例如 {a:1, b:2}

在视图层可以直接使用return返回来的数据

```
{{ a }}
```


# vuex

安装

```
cnpm i vuex -S
```

在 store 目录下，新建 index.js 文件

> 在 Nuxt 中已经对 vuex 进行了处理，所以此处直接导出 state 和 mutations 即可。

```javascript
export const state = ()=>({
    a : 0
})

export const mutations = {
    add(state){
        state.a++
    }
}
```

组件页

```html
<button @click="add">{{a}}</button>
```

```javascript
import { mapState, mapMutations } from 'vuex'

export default {
    computed:{
        ...mapState(['a'])
    },
    methods:{
        ...mapMutations(['add'])
    },
    mounted(){
        console.log( this.$store )
    }
};
```

> 记得重启服务，否则会报 state 不存在这种错误。

# 打包

项目都开发完毕之后，我们需要将开发环境下的碎片化的文件做合并，这个过程就叫做打包（发布）。

我们最终交付上线的是打包后的文件（交付的是生产环境下的代码）。

打包的方法有两种：

- 如果mode=universal（ssr）模式的话，generate可以生成dist目录

```
npm run generate
```

- 如果mode=spa模式的话，build和generate都可以生成dist目录

```
npm run build
```

可以看看 package.json 文件中的 scripts 属性，每个脚本都有特殊的作用。

# 部署

将上一步的 dist 文件夹放入网站服务器下，这样别人就可以通过浏览器直接访问了。

有很多种部署网站的方法，比如 nodejs：

文件结构

```
www/
    node_modules/
    dist/
    app.js
```

app.js 代码

```
// npm install express
const express = require('express');
const app = express();
app.use(express.static('dist'));
app.listen(80);
```

开启网站服务

```
node app.js
```

用户打开浏览器访问这个nodejs站点即可