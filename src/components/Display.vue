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
			@reserveItem="reserveItem"
			@removeItem="removeItem"
			:key="cow.name"
			:name="cow.name"
			:personality="cow.personality"
			:outfit="cow.outfit"
			:happiness="cow.happiness"
			:hunger="cow.hunger"
			:energy="cow.energy"
			:items="items">
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
			itemId: 0,
			items: [
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
		},
		addItem (item) {
			item.id = this.itemId
			this.itemId++
			this.items.push(item)
		},
		placeApple () {
			const left = Math.random() * 95
			const top = Math.random() * 95
			let apple = {
				'name': 'apple',
				'left': left,
				'top': top,
				'foodValue': 30
			}
			this.addItem(apple)
		},
		reserveItem (itemId) {
			let item = this.items.find((item) => item.id == itemId)
			item.reserved = true
		},
		removeItem (itemId) {
			this.items = this.items.filter((item) => item.id != itemId)
		}
	},
	computed: {
		activeCows () {
			let activeCows = this.cows.filter((cow) => { return cow.active })
			return activeCows
		}
	},
	mounted () {
		this.$root.$on('placeApple', this.placeApple)
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
