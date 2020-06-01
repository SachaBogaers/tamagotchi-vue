<template>
  <div id="menu">
		<button class="button" @click="salto" type="button">Salto</button>
		<button class="button" @click="hug" type="button">Hug</button>
		<button class="button" @click="feed" type="button">Feed</button>
		<button class="button" @click="jump" type="button">Jump</button>
  </div>
</template>

<script>


export default {
  name: 'Menu',
  components: {
		
	},
	data () {
		return {
			
		}
	},
	methods: {
		hug () {
			this.$emit('modifyStats', 'happiness', 10)
		},
		feed () {
			this.$emit('modifyStats', 'hunger', 10)
		},
		jump () {
			this.$root.$emit('jump')
		},
		salto () {
			this.$root.$emit('salto')
		}
	},
	computed: {
	
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

</style>
