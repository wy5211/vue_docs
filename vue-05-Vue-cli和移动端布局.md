# vue-cli（重点）

vue-cli 是用来管理 vue 项目的工具，可以使用 vue-cli 快速创建项目、启动项目、编译项目等操作。

vue的单文件组件扩展名是.vue文件，需要借助vue-loader，才能被正常解析。

## vue-cli 3 （新版本）

如果之前安装过低版本的 vue-cli ，那么命令行执行：

```
npm uninstall vue-cli -g
```

然后安装

```
npm install -g @vue/cli
```

然后检查版本

```
vue --version
```

创建项目

```
vue create myapp
```

命令之后后会让我们选择预设：选默认的default即可，Manually指手动自定义，然后选择npm管理包。

```
cd myapp
npm run serve
```

程序开发完毕之后，需要打包

```
npm run build
```

dist目录下的文件，就是生产环境下的文件。



## vue-cli 2 （旧版本）

```bash
# 旧版本：基于 webpack 创建 vue 项目
npm install vue-cli -g
vue init webpack-simple appname  
cd appname
npm install
npm run dev

npm run build
```

## Vue.config.js 的配置

是 vue 项目中的配置页面，需要自己创建。

## proxy 代理

每当使用 npm install 下载依赖模块时，最好加上参数 -S 或 -D

```bash
npm install axios -S
```

```bash
{
    dependencies:{},        # --save        生产环境    简写 -S
    devDependencies:{}      # --save-dev	开发环境    简写 -D
}
```

在项目根目录，自己创建 vue.config.js 文件，里面写入：

```javascript
module.exports = {
    devServer: {
        proxy:{
            "/api": {
                target: "https://api.wyyijiaqin.com",
                pathRewrite: { '^/api': '' },
                changeOrigin: true,
            }
        } 
    }
}
```

那么 vue 中访问 /api 时，实际就是跨域访问 https://api.wyyijiaqin.com 了

```javascript
import axios from "axios";
export default {
    methods:{
        fn(){
            axios.get("/api").then(res=>{
                alert(res.data)
            })
        }
    }
};
```

## alias 别名---对特别长的路径进行缩写

vue.config.js

```javascript
const path = require("path");
function resolve (dir) {
    return path.join(__dirname, dir)
}

module.exports = {
    devServer: {
        proxy:{
            "/api": {
                target: "https://api.wyyijiaqin.com",
                pathRewrite: { '^/api': '' },
                changeOrigin: true,
            }
        } 
    },
    chainWebpack: config=>{
        config.resolve.alias
            .set('@$', resolve('src'))
            .set('assets', resolve('src/assets'))
            .set('$comp', resolve('src/components'))
    }
}
```

使用别名

```javascript
import HelloWorld from "$comp/HelloWorld.vue";
```

> 修改 vue.config.js 后要重新 npm run serve

## sass 环境

```bash
npm install sass-loader node-sass --save-dev
```

然后在vue文件中：

```
<style scoped lang="scss">
$bg : yellow;
body{
    div{
        background: $bg;
    }
}
</style>
```

# json-server

在本地开发测试时，可以使用 json-server 来**模拟数据。**

安装 json-server 命令

```bash
npm install -g json-server
```

在项目中做一个 db.json 的文件

```javascript
{
    "arr" : [
        {"id":1, "name":"刘备"},
        {"id":2, "name":"关羽"},
        {"id":3, "name":"张飞"}
    ]
}
```

命令行执行命令，挂起服务：

> 执行该命令时，一定要注意命令行的位置，如果找不到db.json文件，会自动创建这个文件。

```bash
json-server --watch --port 53000 db.json
```

安装 axios

```bash
npm install axios
```

通过 axios 发起请求

```javascript
axios.post('http://localhost:53000/arr', {name:'赵云'}).then(result=>{result.data})
```

因为 json-server 是 restful 接口，所以请求的 method 是有语义的，get表示获取，post表示添加，put表示修改，delete表示删除。

增删改查

```javascript
// 增 （会自动添加id）
axios.post('http://localhost:53000/arr', {name:'赵云'}).then(result=>{result.data})

// 删 （删掉id为1的这条数据）
axios.delete('http://localhost:53000/arr/1').then(result=>{result.data})

// 改 （把id为2的数据的name改为关二爷）
axios.put('http://localhost:53000/arr/2', {name:'关二爷'}).then(result=>{result.data})

// 查  (查询所有arr数据)
axios.get('http://localhost:53000/arr').then(result=>{result.data})
```