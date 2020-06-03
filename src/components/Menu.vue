<template>
  <div id="menu">
		<button class="button" @click="somersault" type="button">Somersault</button>
		<button class="button" @click="hug" type="button">Hug</button>
		<button class="button" @click="feed" type="button">Feed</button>
		<button class="button" @click="jump" type="button">Jump</button>
		<button class="button" @click="coffee" type="button">Give coffee (cost: 3)</button>
		<button class="button items-button" @click="openItemsMenu" type="button">Open items menu</button>
		<div class="wallet">
			<p>Coins:{{money}}</p>
		</div>
		<ItemsMenu v-show="itemsMenuOpen" :money="money" @spend="spend"/>
  </div>
</template>

<script>
import ItemsMenu from './ItemsMenu.vue'

export default {
  name: 'Menu',
  components: {
		ItemsMenu
	},
	props: {
		delayCoefficient: Number
	},
	data () {
		return {
			money: 0,
			itemsMenuOpen: false
		}
	},
	methods: {
		hug () {
			this.$emit('modifyStats', 'happiness', 10)
		},
		feed () {
			this.$emit('modifyStats', 'hunger', 10)
		},
		coffee () {
			this.$emit('modifyStats', 'energy', 5)
		},
		jump () {
			this.$root.$emit('jump')
		},
		somersault () {
			this.$root.$emit('somersault')
		},
		spend (cost) {
			this.money -= cost
		},
		openItemsMenu() {
			this.itemsMenuOpen = !this.itemsMenuOpen
		}
	},
	computed: {
	
	},
	mounted () {
		setInterval(() =>	this.money += 1, 1000);
	}
}
</script>

<style scoped>
#menu {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
	position: absolute;
	width: 100%;
	background: url('../assets/wood.png');
	bottom: 0;
	height: 10%;
}
.button {
	padding: 15px;
	margin: 10px;
	cursor: pointer;
}

.wallet {
	position: absolute;
	right: 0;
	top: 30%;
	background-color: yellow;
	padding: 5px;
}

.items-button {
	position: absolute;
	right: 15%;
}

</style>
