<template>
  <div id="app">
		<Tamagotchi
			v-for="cow in activeCows"
			:key="cow.name"
			:name="cow.name">
		</Tamagotchi>
  </div>
</template>

<script>
import Tamagotchi from './components/Tamagotchi.vue'

export default {
  name: 'App',
  components: {
		Tamagotchi
	},
	data () {
		return {
			cows: [
				{name: 'Billy', active: true},
				{name: 'Madeliefje'},
				{name: 'Ellie', active: true},
				{name: 'Tommie'},
				{name: 'Pieter'},
				{name: 'Anna'}
			]
		}
	},
	computed: {
		activeCows () {
			let activeCows = this.cows.filter((cow) => { return cow.active })
			return activeCows
		}
	}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
