<template>
  <div id="display">
		<img
			class="item"
			v-for="(item, index) in items"
			:key="'item' + index"
			:src="item_image(item.name)"
			:style="{left: item.left + '%', top: item.top + '%'}">
		<Tamagotchi
			v-for="cow in activeCows"
			@modifyStats="modifyStats"
			:key="cow.name"
			:name="cow.name"
			:sleepiness="cow.sleepiness"
			:personality="cow.personality"
			:stubbornness="cow.stubbornness"
			:appetite="cow.appetite"
			:outfit="cow.outfit"
			:happiness="cow.happiness"
			:hunger="cow.hunger"
			:energy="cow.energy">
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
			items: [
				{
					'name': 'apple',
					'left': 15,
					'top': 20
				}
			]
		}
	},
	methods: {
		modifyStats(type, amount, name) {
			this.$emit('modifyStats', type, amount, name)
		},
		item_image (item_name) {
			let image = require('@/assets/items/' + item_name + '.png')
			return image
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
	position: absolute;
	width: 90%;
	height: 90%;
	background: url('../assets/grass.png');
	top: 0;
	overflow: hidden;
}

.item {
	position: absolute;
}

</style>
