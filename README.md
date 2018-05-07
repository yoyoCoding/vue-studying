# vue-studying
vue 2.0 studying

## 基础指令(内部指令)
### v-text/v-html
在html中输出data中的值,可以使用{{xxx}}这种方式,但这种方式有弊端:网速很慢或者javascript出错时,会暴露{{xxx}},而且这种方式无法渲染html标签。因此使用v-text和v-html来解决
tips:
在生产环境中动态渲染HTML是非常危险的，因为容易导致XSS攻击。所以只能在可信的内容上使用v-html，永远不要在用户提交和可操作的网页上使用。

### v-on
```<button v-on:click="func"></button>```
```<button @click="func"></button>```

### v-model
双向数据绑定(适用于表单input/select/textarea)
修饰符：
``.lazy``  - 取代input监听change事件(焦点离开input时触发change)
``.number``  - input框中只允许输入数字(焦点离开input时触发,若input输入开头字符非数字,则无效)
``.trim`` - 去空格(焦点离开input时触发)

### v-bind
处理html标签属性
<a v-bind:href="url"></a>
<a :href="url"></a>

绑定css样式
``type1:`` 
<div :class="'className'">绑定'className'</div>
``type2:`` 
<div :class="[classA,classB]">绑定定义的classA,classB</div>
``type3:`` 
<div :class="{className:isTrue}">绑定class的判断,isTrue为true,class名为'className'</div>
``type4:`` 
<div :class="[{className:isTrue},classB]">绑定class的判断和定义的classB</div>
``type4:`` 
<div :class="[isTrue?classA:classB]">三元表达式判断</div>
``type5:`` 
<div :style="styleObject">绑定style样式</div>

### v-pre
跳过vue编译,直接显示原始值

### v-cloak
在vue渲染完指定的整个DOM后才进行显示,必须和CSS样式一起使用
[v-cloak] {
  display: none;
}
<div v-cloak>
  {{ message }}
</div>

### v-once
在第一次DOM时进行渲染,渲染完成后视为静态内容,跳出以后的渲染过程

## vue全局API

全局API并不在构造器里，而是先声明全局变量或者直接在Vue上定义一些新功能，Vue内置了一些全局API。说的简单些就是，在构造器外部用Vue提供给我们的API函数来定义新的功能

### (1)Vue.directive
自定义指令
Vue.directive('vname',function(el,binding,vnode){
	//el: 指令所绑定的元素，可以用来直接操作DOM
	//binding:  一个对象，包含指令的信息(如指令名称name,指令绑定的值value,指令的表达式expression)
	//vnode: Vue编译生成的虚拟节点
})
自定义指令有五个生命周期(也叫钩子函数)，分别是 bind,inserted,update,componentUpdated,unbind

``bind:`` 只调用一次，指令第一次绑定到元素时调用，用这个钩子函数可以定义一个绑定时执行一次的初始化动作。
``inserted:`` 被绑定元素插入父节点时调用（父节点存在即可调用，不必存在于document中）。
``update:`` 被绑定于元素所在的模板更新时调用，而无论绑定值是否变化。通过比较更新前后的绑定值，可以忽略不必要的模板更新。
``componentUpdated:`` 被绑定元素所在模板完成一次更新周期时调用。
``unbind:`` 只调用一次，指令与元素解绑时调用。

### (2)Vue.extend
构造器的延伸
Vue.extend 返回的是一个"扩展实例构造器",也就是预设了部分选项的Vue实例构造器。经常服务于Vue.component用来生成组件，可以简单理解为当在模板中遇到该组件名称作为标签的自定义元素时，会自动调用"扩展实例构造器"来生产组件实例，并挂载到自定义元素上。
``tips:`` 
编写完一个扩展实例构造器,需要再进行一次挂载
new yourExtend().$mount('id or tagName');

### (3)Vue.set
全局操作
Vue.set的作用是在构造器外部操作构造器内部的数据、属性或者方法(比如在vue构造器内部定义了一个count为1的数据，我们在构造器外部定义了一个方法，要每次点击按钮给值加1.就需要用到Vue.set)

## Vue的生命周期(钩子函数)
beforeCreate: 初始化之前
created: 创建完成
beforeMount: 挂载之前
mounted: 被挂载之后
beforeUpdate: 数据更新前
updated: 被更新后
activated: activated
deactivated: deactivated
beforeDestroy: 销毁之前
destroyed: 销毁之后

## Template
``type1:`` 适用简短html代码
template: `<h4 style="color:red">我是选项模板</h4>`

``type2:`` 适用外部引用模版
<template id="demo2">
	<h4 style="color:red">我是标签模板</h4>
</template>
template: '#demo2'

``type3:`` 适用较多的外部引用模板
<script type="x-template" id="demo3">
	<h4 style="color:red">我是script标签模板</h4>
</script>
template: '#demo3'

## component
必须在vue作用域里使用

### (1)全局化注册组件
Vue.component('name',{
	template: ``
})

### (2)局部注册组件
components:{
	'name' : {
		tempalte:``
	}
}

### (3)组件属性props
props:['propsname']
可直接在template的模版中使用{{propsname}},获得属性值(-分隔的属性名,在props写成驼峰式：prop-name->propsName)

### (4)父子组件
父子组件嵌套
构造器外定义：
var childcom = {
	template: `<div>我是子组件</div>`
}
var parentcom = {
	template: `<div><p>我是父组件</p><childcom></childcom></div>`,
	components: {
		'city': city
	}
}
构造器内设置：
components: {
	'parentcom': parentcom
}

### (5)组件标签<component>
<component></component>标签是Vue框架自定义的标签，它的用途就是可以动态绑定我们的组件，根据数据的不同更换不同的组件。

## Vue选项
### propsData
propsData不是和属性有关，他用在全局扩展时进行传递数据(Vue.extend)
propsData在实际开发中我们使用的并不多，它的作用就是在单页应用中保持状态和数据的
step1:
在全局扩展里加入props进行接收propsData:{a:1}

step2:
传递时用propsData进行传递props:['a']

step3:
用插值的形式写入模板{{ a }}

### computed 计算选项
computed的作用主要是对原数据进行改造输出。改造输出：包括格式的编辑，大小写转换，顺序重排，添加符号等






