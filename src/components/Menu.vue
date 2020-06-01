<template>
  <div id="menu">
		<button type="button">Salto</button>
		<button @click="hug" type="button">Hug</button>
		<button @click="feed" type="button">Feed</button>
		<button type="button">Jump</button>
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
	background-color: brown;
	bottom: 0;
	height: 10%;
}

</style>
