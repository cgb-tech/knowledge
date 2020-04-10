### vue指令

- v-once : 只被执行一次的指令
- v-for : 需要加一个表达式
- v-html : 展示html标签
- v-pre : 原封不动的展示
- v-cloak : 斗篷 [v-cloak]{display:none;}
- v-bind : 缩写(:)
- v-on : (@) $event
  - @click.stop : 防止冒泡 
  - @click.prevent : 阻止默认事件
  - @keyup.enter=""
  - .once : 只触发一次

### 计算属性

- computed ：计算属性 有缓存
  - for(let i in books)
  - for(let i of books)

### js高阶函数

- filter
- map
- reduce

### 修饰符

- .lazy 
- .number
- .trim

### 组件化

- ```vue
  Vue.extend({
      template: ` `,
      components: {
          cpn:cpn
      }
  })
  ```

- 语法糖

  ```vue
  Vue.component('con1',{
  	template: `
  		<div></div>		
  	`
  })
  ```

  组件中的数据保存在自己的data中，data在这里是一个函数，保证了每次返回的数据对象都是一个新的数据对象，来保证多个组件不共享同一组数据