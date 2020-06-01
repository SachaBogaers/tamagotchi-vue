<template>
  <div id="app">
		<Display 
			:cows="cows"
			@modifyStats="modifyStats"
		/>
		<Menu 
			@modifyStats="modifyStats"
		/>
  </div>
</template>

<script>
import Display from './components/Display.vue'
import Menu from './components/Menu.vue'

export default {
  name: 'App',
  components: {
		Display,
		Menu
	},
	data () {
		return {
			cows: [
				{
					name: 'Cowboy Billy Boem', active: true, sleepiness: 5, speed: 7, size: 1, neediness: 2, appetite: 7,
					happiness: 100, hunger: 100,
					outfit: {
						hat: {
							name: 'cowboy',
							left: '-1',
							top: '-5'
						}
					}
				},
				{
					name: 'Mawiefje', active: true, sleepiness: 0.5, speed: 10, size: 1, neediness: 8, appetite: 10,
					happiness: 100, hunger: 100,
					outfit: {
						hat: {
							name: 'cowboy',
							left: '-1',
							top: '-5'
						},
						glasses: 'sunglasses'
					}
				},
				{
					name: 'Ellie de Scheve', active: true, sleepiness: 7, speed: 5, size: 1, neediness: 10, appetite: 6,
					happiness: 100, hunger: 100,
					outfit: {
						hat: ''
					}
				},
				{
					name: 'TomTomTommie', active: true, sleepiness: 4, speed: 8, size: 1, neediness: 10, appetite: 9,
					happiness: 100, hunger: 100,
					outfit: {
						hat: ''
					}
				},
				{
					name: 'Pietertje', active: true, sleepiness: 3, speed: 4, size: 1, neediness: 4, appetite: 4,
					happiness: 100, hunger: 100,
					outfit: {
						hat: ''
					}
				},
				{
					name: 'SUPERAnna', active: true, sleepiness: 10, speed: 3, size: 2, neediness: 1, appetite: 2,
					happiness: 100, hunger: 100,
					outfit: {
						hat: ''
					}
				},
				{
					name: 'Jochem', active: true, sleepiness: 0, speed: 3, size: 2, neediness: 10, appetite: 8,
					happiness: 100, hunger: 100,
					outfit: {
						hat: '',
						glasses: 'sunglasses'
					}
				}
			],
		}
	},
	methods: {
		startDepletions () {
			this.cows.forEach((cow) => {
				setInterval(() => {
					cow.happiness = Math.max(0, cow.happiness - 1)
				}, 1000 + (11 - cow.neediness) * 300)
				setInterval(() => {
					cow.hunger = Math.max(0, cow.hunger - 1)
				}, 1000 + (11 - cow.appetite) * 400)
			})
		},
		modifyStats(type, amount, name) {
			if (name) {
				let cow = this.cows.find((cow) => { return cow.name == name })
				if (!cow) return
				if (type == 'happiness') cow.happiness += amount
				else if (type == 'hunger') cow.hunger += amount
			} else {
				this.cows.forEach((cow) => {
					if (type == 'happiness') cow.happiness += amount
					else if (type == 'hunger') cow.hunger += amount
				})
			}
		}
	},
	mounted () {
		this.startDepletions()
		this.cows.forEach((cow) => {
			cow.happiness = 100
			cow.hunger = 10
		})
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
  margin-top: 0px;
	height: 100%;
	width: 100%;
	position: absolute;
}
</style>
