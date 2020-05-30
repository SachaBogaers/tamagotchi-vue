<template>
  <div id="display">
		<Tamagotchi class ="tamagotchi"
			v-for="cow in activeCows"
			:key="cow.name"
			:name="cow.name"
			:sleepiness="cow.sleepiness"
			:speed="cow.speed"
			:neediness="cow.neediness"
			:command="command">
		</Tamagotchi>
  </div>
</template>

<script>
import Tamagotchi from './Tamagotchi.vue'

export default {
  name: 'Display',
  components: {
		Tamagotchi
	},
	data () {
		return {
			cows: [
				{name: 'Billy', active: true, sleepiness: 5, speed: 7, size: 1, neediness: 2},
				{name: 'Madeliefje', active: true, sleepiness:0, speed: 10, size: 1, neediness: 8},
				{name: 'Ellie', active: true, sleepiness: 6, speed: 5, size: 1, neediness: 10},
				{name: 'Tommie', active: true, sleepiness: 2, speed: 8, size: 1, neediness: 10},
				{name: 'Pieter', active: true, sleepiness: 0.5, speed: 4, size: 1, neediness: 4},
				{name: 'Anna', active: true, sleepiness: 10, speed: 3, size: 2, neediness: 1}
			],
			command: ''
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
#display {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
	margin-top: 0px;
	position: absolute;
	width: 100%;
	height: 100%;
	padding-bottom: 35px;
	background: #00F260;  /* fallback for old browsers */
	background: -webkit-linear-gradient(to bottom, #0575E6, #00F260);  /* Chrome 10-25, Safari 5.1-6 */
	background: linear-gradient(to bottom, #0575E6, #00F260); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}

</style>
