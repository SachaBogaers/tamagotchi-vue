<template>
  <div id="display">
		<Tamagotchi class ="tamagotchi"
			v-for="cow in activeCows"
			:key="cow.name"
			:name="cow.name">
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
				{name: 'Billy', active: true},
				{name: 'Madeliefje', active: true},
				{name: 'Ellie', active: true},
				{name: 'Tommie', active: true},
				{name: 'Pieter', active: true},
				{name: 'Anna', active: true}
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
