# Vue的前置支持

## 安装node.js

1. 先 [下载node.js](https://nodejs.org/dist/v10.15.0/node-v10.15.0-x64.msi,node.js官网)。
2. 点击下一步安装，无脑下一步。
3. 设置环境变量，在系统环境变量的path下添加node.js的根路径。
4. 打开cmd输入`node -v`命令，如果返回版本信息表示安装成功。

## 设置node.js

1. 设置全模块和缓存放在node.js安装路径下,打开cmd输入以下命令

```shell
npm config set prefix "D:\Develop\nodejs\node_global"
npm config set cache "D:\Develop\nodejs\node_cache"
```

2. 设置npm镜像为淘宝。

```shell
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

3. 设置cnpm命令替换npm命令，在path中添加`D:\develop\nodejs\node_global`路径。

## 安装vue.cli

```shell
cnpm install -g vue-cli
```

cnpm：cnpm插件。

install：安装命令。

-g：全局安装。

vue-cli：需要安装的插件。

# Vue的基本命令

## `v-cloak`：无数据不展示内容

用于解决因数据未加载而显示插值表达式的问题

```vue
<style>
    [v-cloak] {
    	dispaly: none
    }
</style>

<p v-cloak>{{ msg }}</p>
```

## `v-text`：替换文本

替换标签里面的内容,所有内容都解析为字符串。

```vue
<p v-text="msg"></p>
```

## `v-html`：替换HTML

替换标签里面的内容，所有内容作为html解析。

```vue
<p v-html="msg"></p>
```

## `v-on`：绑定事件

绑定事件，需要些在methods属性中，可以简写为`@click`，在methods中如果要修改data的指必须使用`this`关键字，this.属性名代表获取或者修改该属性。

```vue
<el-button @click="replace">{{ msg }}</button>

var vue = new Vue({
    el: '#app',
    data: {
        msg: '消息',
        mytitle: '按钮'
    },
    methods:{
        replace(){
      	  this.mytitle = "123"
        }
    }
})
```

## 事件修饰符

### `stop`：阻止冒泡事件

阻止当前标签外的其他标签的事件。下面的点击事件如果不加`stop`的话只要点击Button就会触发两个点击事件，如果加了`stop`那么除了button标签的点击事件会触发外其他的事件都不会触发。

```vue
<div @click="divClick" class="inner"> 
	<button @click.stop="buttonClick">按钮</button>
</div>
```

### `prevent`：阻止标签默认行为

阻止标签原有默认方法，下面的点击事件如果不加`prevent`的话点击之后会跳到百度页，如果加了`prevent`的话只会触发点击事件而不会触发跳转。

```vue
<a href="http://www.baidu.com" @click.prevent="ale">去百度</a>
```

### `capture`：捕获事件

事件从当前标签开始往下执行子标签的事件，下面的点击事件如果不加`capture`的话点击之后会先执行子标签在执行外层标签，如果加了`capture`那么事件在进来时就被捕获先执行自己才执行子标签的。

```vue
<div @click.capture="divClick" class="inner"> 
	<button @click="buttonClick">按钮</button>
</div>
```

### `self`：只在点击自身的时候触发

只有当点击当前事件的标签的时候才会触发事件。如果在div的点击事件上加了`self`的话，那么点击button是不会触发div的点击事件的，只有点击div本身才会触发点击事件。

```vue
<div @click.self="divClick" class="inner"> 
	<button @click="buttonClick">按钮</button>
</div>
```

### `once`：只触发一次

绑定的事件只执行一次。

```vue
<a href="http://www.baidu.com" @click.prevent.once="ale">去百度</a>
```





## `v-bind`：属性绑定

### 绑定属性：

该命令使标签属性的值作为js解析，可以省略为`:`但是必须是英文状态下的。

```vue
<el-button :title="mytitle">{{ msg }}</button>
```

### 绑定class：

以数组的形式，把class绑定到当前标签，可以使用三元表达式、对象的方式绑定。

三元表达式：

```vue
<el-input v-model="msg"  :class = [true? 'class':'']></el-input>
```

对象：

```vue
<el-input v-model="msg"  :class = {'class':'true'}></el-input>
```



## `v-model`：双向数据绑定

实现数据的双向绑定，只能用在表单元素中。

```vue
<el-input v-model="msg" />
```

# 过滤器

## 全局过滤器

定义方式：定义在vue实例外面，跟`new Vue()`同级。

```vue
Vue.filter('过滤器名',function(data){
	return data
})
```

生效范围：所有Vue实例都能引用。

## 私有过滤器

定义方式：定义在vue实例中，跟实例中的`data`属性同级。

```vue
var vue = new Vue({
    el:'#app',
    data:{
    	msg:'双向数据绑定'
    },
    filters:{
        replace1:function(msg){
        	return msg+"123";
        }
    }
})
```



生效范围：当前vue实例范围内。

## 注意

过滤器调用采用就近原则，如果同名先调用自己私有的。



# 自定义按键修饰符

在vue中默认系统提供了一些按键修饰符的配置，但是不是所有的都配置了 所以需要自己进行按键修饰符的配置。

系统提供的按键修饰符：`.enter`、`.tab`、`.delete `、`.esc`、`.space`、`.up`、`.down`、`.left`、`.right`、`.ctrl`、`.alt`、`.shift`、`.meta`。

自定义别名：`Vue.config.keyCodes.键序号`

使用：`@keyUp.entity`回车键弹起触发事件。

# 自定义指令

在vue中有时候系统提供给我们的指令并不能满足我们的项目要求，所以有时候我们需要使用到Vue提供给我们的自定义指令，自定义指令分为全局和私有化的。

## 全局指令定义：

```vue
Vue.directive('指令名称',{
    阶段:function(参数){
    }
})
```

## 私有化指令定义：

在当前vue实例中的`directives`参数中定义。

```vue
var vue = new Vue({
    el:'#app',
    directives:{
        '指令名称':{
            阶段:function(el){
            	el.focus();
            }
        }
    }

})
```

自定义指令阶段：

bind：只调用一次，指令在绑定到元素的时候进行一次初始化调用。

inserted：被绑定元素在插入的时候进行调用，可以理解为初始化的时候调用。

update：所在组件的虚拟节点更新的时候调用，但是可能在虚拟子节点更新之前调用。

componentUpdated：指令在所有虚拟节点更新完成后调用。

unbind：只调用一次，在元素接触绑定的时候进行调用。

参数：

el：当前绑定的DOM元素。

binding：对象，包含属性

   	1. `name`：指令名称。
   	2. `value`：指令的值，计算后的结果。如：`v-my-directive="1 + 1"` 中绑定的值为`2`。
   	3. `oldValue`：指令绑定的前一个值，仅在`update`和`componentUpdated`里可用。
   	4. `expression`：字符串形式的参数。

# 组件定义

## 全局组件定义

定义方式:

```vue
<!-- 引入组件 -->
import 定义的组件名称 from "组件地址"
<!-- 注册全局组件 -->
Vue.component("将来当标签使用的名字"，定义的组件名)
```

全局组件可以在任何地方使用，但是不能和系统标签重名，否则会失效。

## 私有化组件

定义方式：

```
<!-- 在script中引入组件-->
import 定义的组件名称 from "组件地址"
<!-- 在对象实例中注册组件 -->
<!--方式一 -->
component:{
  定义的组件名称  
}
<!-- 方式二 -->
components:{
    将来当做标签的名字:定义的组件名
}

```

