<template>
  <div id="menu">
		<button class="button" @click="salto" type="button">Salto</button>
		<button class="button" @click="hug" type="button">Hug</button>
		<button class="button" @click="feed" type="button">Feed</button>
		<button class="button" @click="jump" type="button">Jump</button>
		<button class="button" @click="coffee" type="button">Give coffee (cost: 3)</button>
		<button class="button" @click="placeApple" type="button">Place apple (cost: 5)</button>
		<div class="wallet">
			<p>Coins:{{money}}</p>
		</div>
  </div>
</template>

<script>


export default {
  name: 'Menu',
  components: {
		
	},
	props: {
		delayCoefficient: Number
	},
	data () {
		return {
			money: 0
			
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
			if (this.spend(3)) {
				this.$emit('modifyStats', 'energy', 5)
			}
		},
		placeApple (){
			if (this.spend(5)) {
				this.$root.$emit('placeApple')
			}
		},
		jump () {
			this.$root.$emit('jump')
		},
		salto () {
			this.$root.$emit('salto')
		},
		spend (cost) {
			if (this.money < cost) {
				alert('U HAVE NO MONNIE')
				return false
			} else {
				this.money -= cost
				return true
			}
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
}

.wallet {
	position: absolute;
	right: 0;
	top: 10%;
	background-color: yellow;
	padding: 15px;
}

</style>
