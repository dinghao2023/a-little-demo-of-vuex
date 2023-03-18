### Vuex

![](https://vuex.vuejs.org/vuex.png)

actions、mutations、state皆为对象，且由store管理。

##### 1.概念

在Vue中实现==集中式状态（数据）管理==的一个==Vue插件==，对vue应用中多个组件的共享状态（数据）进行集中式的管理（读/写），也是一种组件间通信的方式，且

适用于任意组件间通信。

github地址：https://github.com/vuejs/vuex

##### 2.何时使用？

1.多个组件依赖同一状态（数据）

2.来自不同组件的行为需要变更同一状态（数据）

即：**多个组件需要共享数据时。**

##### 3.搭建vuex环境

1.安装vuex

vue2中，要用vuex的3版本；vue3中，要用vuex的4版本

`npm i vuex@3`	安装vuex的3版本

2.使用

因为vuex是vue中的一个插件，所以通过`Vue.use()`使用。之后创建vm时就可以传入store配置项。



1. 创建文件：`src/store/index.js`：

   该文件用于创建vuex中最为核心的store。

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入Vuex
   import Vuex from 'vuex'
   //应用Vuex插件
   Vue.use(Vuex)
   
   //准备actions对象——响应组件中用户的动作
   const actions = {}
   //准备mutations对象——修改state中的数据
   const mutations = {}
   //准备state对象——保存具体的数据
   const state = {}
   
   //创建并暴露store
   export default new Vuex.Store({
   	actions,
   	mutations,
   	state
   })
   ```

2. 在```main.js```中创建vm时传入```store```配置项

   ```js
   //引入Vue核心库
   import Vue from 'vue'
   //引入App组件
   import App from './App.vue'
   //引入store，store文件夹下的index.js可省略
   import store from './store'
   
   //关闭Vue的生产提示
   Vue.config.productionTip = false
   
   //创建vm
   new Vue({
   	el:'#app',
   	render: h => h(App),
   	store
   })
   ```

##### 4.基本使用

1. 初始化数据、配置```actions```、配置```mutations```，操作文件`store.js`

   src/store/index.js文件：

   ```js
   import Vue from 'vue'
   import Vuex from 'vuex'
   Vue.use(Vuex)
   
   const actions = {
   	jia(context,value){
   		// console.log('actions中的jia被调用了',miniStore,value)
   		context.commit('JIA',value)
   	},
       jian(context,value) {
           context.commit("JIAN",value)
       },
       jiaOdd(context, value) {
           if(context.state.sum % 2) {
               context.commit("JIA", value)
           }
       },
       jiaWait(context, value) {
           setTimeout(() => {
               context.commit("JIA", value)
           },500)
       }
   }
   const mutations = {
   	JIA(state,value){
   		// console.log('mutations中的JIA被调用了',state,value)
   		state.sum += value
   	},
       JIAN(state,value) {
           state.sum -= value
       }
   }
   //初始化数据
   const state = {
      sum:0
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	actions,
   	mutations,
   	state,
   })
   ```

   入口文件main.js：

   ```js
   import Vue from 'vue'
   import App from './App.vue'
   import store from "./store"
   //关闭Vue的生产提示
   Vue.config.productionTip = false
   
   //创建vm
   new Vue({
   	el:'#app',
   	render: h => h(App),
   	store,
   	beforeCreate() {
   		Vue.prototype.$bus = this
   	}
   })
   ```

   子组件Count.vue：

   ```vue
   <template>
   	<div>
   		<h2>当前求和为：{{ $store.state.sum }}</h2>
           <select v-model="n">
   			<option :value="1">1</option>
   			<option :value="2">2</option>
   			<option :value="3">3</option>
   		</select>
   		<button @click="add">+</button>
           <button @click="minus">-</button>
   		<button @click="oddAdd">当前和为奇数再加</button>
   		<button @click="waitAdd">等一等再加</button>
   	</div>
   </template>
   <script>
   	export default {
   		name:'Count',
   		data() {
   			return {
   				n:1
   			}
   		},
   		methods: {
   			add() {
   				this.$store.dispatch("jia",this.n)
   			},
               minus() {
   				this.$store.dispatch("jian",this.n)
   			},
   			oddAdd() {
   				this.$store.dispatch("jiaOdd",this.n)
   			},
   			waitAdd() {
   				this.$store.dispatch("jiaWait", this.n)
   			}
   		}
   	}
   </script>
   ```

   由于store.js中actions配置对象中的jia、jian只是起到传递给mutations的作用，所以可注释掉actions中的jia、jian回调函数，在组件Count.vue中直接进行如下调用，直接通过commit与mutations对话：

   ```js
   add() {
       this.$store.commit("JIA",this.n)
   },
   minus() {
   	this.$store.commit("JIAN",this.n)
   }
   ```

2. 组件中读取vuex中的数据：`$store.state.xxxx`

3. 组件中修改vuex中的数据：```$store.dispatch('action中的方法名',数据)``` 或 ```$store.commit('mutations中的方法名',数据)```

   >备注：若没有网络请求或其他业务逻辑，组件中也可以越过actions，即不写```dispatch```，直接编写```commit```

##### 5.getters的使用

1. 概念：当state中的数据需要经过加工后再使用时，可以使用getters加工。类似于计算属性。

2. 在```store.js```中追加```getters```配置

   ```js
   ......
   
   const getters = {
       //注意传入参数state
   	bigSum(state){
   		return state.sum * 10
   	}
   }
   
   //创建并暴露store
   export default new Vuex.Store({
   	......
   	getters
   })
   ```

3. 组件中插值语法读取数据：`$store.getters.bigSum`

##### 6.四个map方法的使用

​	在组件Count.vue组件中引入`mapState,mapGetters,mapActions,mapMutations`：

```js
import {mapState,mapGetters,mapActions,mapMutations} from "vuex"
```

1. <strong>mapState方法：</strong>用于帮助我们映射```state```中的数据为计算属性

   ```js
   computed:{
       // sum() {
       // 		return this.$store.state.sum
       // },
       // school() {
       // 		return this.$store.state.school
       // },
       // subject() {
       // 		return this.$store.state.subject
       // },
       //借助ES6的扩展运算符将上面注释部分等价以下代码（对象写法）：
   	...mapState({sum:"sum",school:"school",subject:"subject"})
       //借助ES6的扩展运算符将上面注释部分等价以下代码（数组写法）：
       ...mapState(["sum","school","subject"])
   }
   ```

2. <strong>mapGetters方法：</strong>用于帮助我们映射```getters```中的数据为计算属性

   ```js
   computed: {
       // bigSum() {
       // 		return this.$store.getters.bigSum
       // },
       //借助mapGetters生成计算属性：bigSum（对象写法）
       ...mapGetters({bigSum:'bigSum'}),
       //借助mapGetters生成计算属性：bigSum（数组写法）
       ...mapGetters(['bigSum'])
   },
   ```

3. <strong>mapActions方法：</strong>用于帮助我们生成与```actions```对话的方法，即：包含```$store.dispatch(xxx)```的函数

   ```vue
   <script>
       ...
       methods:{
           //oddAdd() {
           //	this.$store.dispatch("jiaOdd",this.n)
           //},
   		//waitAdd() {
   		//	this.$store.dispatch("jiaWait", this.n)
   		//}
           //靠mapActions方法替换以上代码（对象形式）
           ...mapActions({oddAdd:"jiaOdd",waitAdd:"jiaWait"})
           //若用数组写法，则methods中的方法名和actions中的方法名相同
   		...mapActions(['jiaOdd','jiaWait'])
       }
   </script>
   
   //在vue组件模板中创建方法时传入数据n
   <button @click="oddAdd(n)">当前和为奇数再加</button>
   <button @click="waitAdd(n)">等一等再加</button>
   ```

4. <strong>mapMutations方法：</strong>用于帮助我们生成与```mutations```对话的方法，即：包含```$store.commit(xxx)```的函数

   ```vue
   <script>
       ...
       methods: {
           // add() {
           // 		this.$store.commit("JIA",this.n)
           // },
           // minus() {
           // 		this.$store.commit("JIAN",this.n)
           // },
           //若要通过mapMutations生成方法替换以上注释部分代码，除了下面代码还需在vue组件模板中创建方法时传入数据n，即@click="add(n)"（对象写法）
           ...mapMutations({add:"JIA",minus:"JIAN"}),
           //若用数组写法，则methods中的方法名和mutations中的方法名相同
           ...mapMutations(['JIA','JIAN']),
   	}
   </script>
   
   //在vue组件模板中创建方法时传入数据n，即@click="add(n)"
   <button @click="add(n)">+</button>
   <button @click="minus(n)">-</button>
   ```

    > 备注：mapActions与mapMutations使用时，若需要传递参数需要：在模板中绑定事件时传递好参数，否则参数是事件对象。

##### 7.模块化+命名空间

1. 目的：让代码更好维护，让多种数据分类更加明确。

2. 修改```store.js```

   ```javascript
   const countAbout = {
     namespaced:true,//开启命名空间，使mapState、mapGetters能够从对应模块当中读取
     state:{x:1},
     mutations: { ... },
     actions: { ... },
     getters: {
       bigSum(state){
          return state.sum * 10
       }
     }
   }
   
   const personAbout = {
     namespaced:true,//开启命名空间
     state:{ ... },
     mutations: { ... },
     actions: { ... }
   }
   
   const store = new Vuex.Store({
     modules: {
       countAbout,
       personAbout
     }
   })
   ```

3. 开启命名空间后，组件中读取state数据：

   ```js
   //方式一：自己直接读取
   this.$store.state.personAbout.list
   //方式二：借助mapState读取：
   ...mapState('countAbout',['sum','school','subject']),
   ```

4. 开启命名空间后，组件中读取getters数据：

   ```js
   //方式一：自己直接读取
   this.$store.getters['personAbout/firstPersonName']
   //方式二：借助mapGetters读取：
   ...mapGetters('countAbout',['bigSum'])
   ```

5. 开启命名空间后，组件中调用dispatch

   ```js
   //方式一：自己直接dispatch
   this.$store.dispatch('personAbout/addPersonWang',person)
   //方式二：借助mapActions：
   ...mapActions('countAbout',{incrementOdd:'jiaOdd',incrementWait:'jiaWait'})
   ```

6. 开启命名空间后，组件中调用commit

   ```js
   //方式一：自己直接commit
   this.$store.commit('personAbout/ADD_PERSON',person)
   //方式二：借助mapMutations：
   ...mapMutations('countAbout',{increment:'JIA',decrement:'JIAN'}),
   ```