# vue新建项目

> vue init webpack sell
>
> cd sell
>
> npm install
>
> npm run dev 

Vue项目结构

![结构.png](https://i.loli.net/2019/09/28/w3BTioVp8zqtv9L.png)



* components
  * 写前端页面具体内容
* router
  * 配置页面跟url的对应

# Vue组件重要选项

```vue
new Vue({
	data:{
		a:1,
		b:[]
	},
	methods:{
		dosomething:function(){
			this.a ++
		}
	},
	watch:{
		'a':function(val,oldVal){
			console.log(val,oldVal)
		}
	}
})
```

* `data`
* `methods`
* `watch`监听

# 模板指令

* 数据渲染
  * `v-text`
    * `{{}}` 简写
  * `v-html`

* 控制模块隐藏
  * v-if
  * v-show

* 循环列表

  * `v-for`

    ```vue
    <ul>
        <li v-for='item in items'>
            <p v-text='item.label'></p>
        </li>
    </ul>
    data:{
    	items:[
    		{
    			label:'apple'
    		},
    		{
    			label:'banana'
    		}
    	]
    },
    ```

* 事件绑定

  * `v-on`

    ```vue
    <button v-on:click="doThis"></button>
    //简写
    <button @click="doThis"></button>
    
    methods:{
    	doThis:function(someThinf){}
    }
    ```

* 属性绑定

  * `v-bind`

    ```vue
    <img v-bind:src="imageSrc">
    <div :class="{red: osRed}">
        
    </div>
    ```

    