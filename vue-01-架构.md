# MVC架构

- M 层 （ Model ） 模型
- V 层 （ View ） 视图
- C 层 （ Controller ） 控制器

```html
<input type="text" id="input1" autocomplete="off">
<span id="span1"></span>
<script>
    var M = {
        a : "",
        set( v ){
            M.a = v;
            V.render();
        },
        get(){
            return M.a;
        }
    }

    var V = {
        init(){
            input1.onkeyup = C.keyup;
        },
        render(){
            span1.innerHTML = M.get();
        }
    }

    var C = {
        keyup(){
            M.set( this.value )
        }
    }
    
    V.init();

</script>
```

# MVP架构

- M 层 （ Model ） 模型
- V 层 （ View ） 视图
- P 层 （ Presenter ） 主持人

```html
<input type="text" id="input1" autocomplete="off">
<span id="span1"></span>
<script>
    var M = {
        a : "",
        set( v ){
            M.a = v;
            P.render();
        },
        get(){
            return M.a;
        }
    }

    var V = {
        init(){
            input1.onkeyup = P.keyup;
        },
        render(){
            span1.innerHTML = P.get();
        }
    }

    var P = {
        keyup(){
            M.set( this.value );
        },
        render(){
            V.render();
        },
        get(){
            return M.get();
        }
    }
    
    V.init();

</script>
```

# MVVM架构（重点）

- M 层 （ Model ） 模型
- V 层 （ View ） 视图
- VM 层 （ View Model ） 视图模型

## 数据绑定原理（重点）

Object.defineProperty

```
<input type="text" id="input1" autocomplete="off">
<span id="span1"></span>
<script>
    var M = {
        a : ""
    }
    var V = {

    }
    var VM = {
        init(){
            input1.onkeyup = function(){
                M.a = this.value;
                VM.observer(M, "a");
            }
        },
        observer(obj, attr){
            var val = obj[attr];
            Object.defineProperty(obj, attr, {
                get(){
                    return val;
                },
                set( v ){
                    input1.value = span1.innerHTML = val = v;
                }
            })
        }
    }

    VM.init();
</script>
```

## vm 核心层

```
<input type="text" v-model="a" />
<span v-text="a"></span><br>
{{a}}

<script>
var M = {
    a : ""
}
var V = {

}
var VM = {
    nodeList:{},
    init(){
        var nodes = document.body.childNodes;
        for( var i=0,len=nodes.length; i<len; i++ ){
            var node = nodes[i];
            VM.compile( node );
        }     
    },
    compile( node ){
        var name = "";
        if( node.nodeType === 1 ){
            // 元素节点
            var attrs = node.attributes;
            for( var i=0,len=attrs.length; i<len; i++ ){
                var nodeName = attrs[i].nodeName;
                var nodeValue = attrs[i].nodeValue;
                if( nodeName === "v-model" ){
                    name = nodeValue;
                    // 数据双向绑定
                    node.addEventListener("keyup", function(){
                        M[name] = this.value;
                    })
                    VM.observer(M, name);
                }
                if( nodeName === "v-text" ){
                    name = nodeValue;
                }
            }               
        }
        if( node.nodeType === 3 ){
            // 文本节点
            var arr = node.nodeValue.match(/{{(.+)}}/);
            if( arr ){
                name = arr[1];
            }
        }
        // name表示M层中的某个数据，node表示这个数据关联的节点
        if( name ){
            //console.log( name, node );
            if( VM.nodeList[name] == undefined ){
                VM.nodeList[name] = [];
            }
            VM.nodeList[name].push( node );
        }
    },
    observer( obj, attr ){
        var val = obj[attr];
        Object.defineProperty(obj, attr, {
            set( v ){
                val = v;
                // 视图层更新
                var arr = VM.nodeList[attr];
                for( var i=0,len=arr.length; i<len; i++ ){
                    var node = arr[i];
                    if( node.nodeType === 1 ){
                        if( node instanceof HTMLInputElement ){
                            node.value = v;
                        }else{
                            node.innerHTML = v;
                        }
                    }
                    if( node.nodeType === 3 ){
                        node.nodeValue = v;
                    }
                }
            },
            get(){
                return val;
            }
        })
    }
}

VM.init();
</script>
```



