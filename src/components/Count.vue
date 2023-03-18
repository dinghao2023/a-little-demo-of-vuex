<template>
	<div>
		<h2>当前求和为：{{ sum }}</h2>
		<h3>当前求和10倍为：{{ bigSum }}</h3>
		<h3>我在{{ school }}学习{{ subject }}</h3>
		<h3 style="color:red">下方组件的总人数是：{{ $store.state.personOptions.personList.length }}</h3>
		<select v-model="n">
			<option :value="1">1</option>
			<option :value="2">2</option>
			<option :value="3">3</option>
		</select>
		<button @click="add(n)">+</button>
		<button @click="minus(n)">-</button>
		<button @click="oddAdd(n)">当前和为奇数再加</button>
		<button @click="waitAdd(n)">等一等再加</button>
	</div>
</template>

<script>
	import { mapState,mapGetters,mapActions,mapMutations } from 'vuex'

	export default {
		name:'Count',
		data() {
			return {
				n:1
			}
		},
		computed:{
			// sum() {
			// 	return this.$store.state.sum
			// },
			// school() {
			// 	return this.$store.state.school
			// },
			// subject() {
			// 	return this.$store.state.subject
			// },
			// ...mapState({sum:"sum",school:"school",subject:"subject"})
			...mapState("countOptions",["sum","school","subject"]),
			...mapState("personOptions",["personList"]),
			// bigSum() {
			// 	return this.$store.getters.bigSum
			// },
			...mapGetters("countOptions",["bigSum"])
		},
		methods: {
			// add() {
			// 	this.$store.commit("JIA",this.n)
			// },
			// minus() {
			// 	this.$store.commit("JIAN",this.n)
			// },
			...mapMutations("countOptions",{add:"JIA",minus:"JIAN"}),
			// oddAdd() {
			// 	this.$store.dispatch("jiaOdd",this.n)
			// },
			// waitAdd() {
			// 	this.$store.dispatch("jiaWait", this.n)
			// }
			...mapActions("countOptions",{oddAdd:"jiaOdd",waitAdd:"jiaWait"})
		}
	}
</script>

<style scoped>
	button{
		margin-left: 5px;
	}
</style>