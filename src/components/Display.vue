<template>
  <div id="display">
		<Tamagotchi
			v-for="cow in activeCows"
			@modifyStats="modifyStats"
			:key="cow.name"
			:name="cow.name"
			:sleepiness="cow.sleepiness"
			:speed="cow.speed"
			:neediness="cow.neediness"
			:appetite="cow.appetite"
			:outfit="cow.outfit"
			:happiness="cow.happiness"
			:hunger="cow.hunger">
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
	props: {
		cows: Array,
	},
	data () {
		return {
		}
	},
	methods: {
		modifyStats(type, amount, name) {
			this.$emit('modifyStats', type, amount, name)
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
	position: absolute;
	width: 100%;
	height: 90%;
	background: url('../assets/grass.png');
	top: 0;
	overflow: hidden;
}

</style>
