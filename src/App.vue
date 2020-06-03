<template>
  <div id="app">
		<Display 
			:cows="cows"
			@modifyStats="modifyStats"
		/>
		<Menu 
			@modifyStats="modifyStats"
			:delayCoefficient="delayCoefficient"
		/>
		<List 
			:cows="cows"
		/>
  </div>
</template>

<script>
import Display from './components/Display.vue'
import Menu from './components/Menu.vue'
import List from './components/List.vue'

export default {
  name: 'App',
  components: {
		Display,
		Menu,
		List
	},
	data () {
		return {
			appSpeed: 0, // Must be in [0, 2).
			cows: [
				{
					name: 'Cowboy Billy Boem', active: true, size: 1,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: {
							name: 'cowboy',
							left: '-1',
							top: '-5'
						}
					},
					personality: {
						fitness: 7,
						neediness: 2,
						speed: 5,
						stubbornness: 7,
						appetite: 7,
						sleepiness: 5
					}
				},
				{
					name: 'Mawiefje', active: true, size: 1,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: {
							name: 'cowboy',
							left: '-1',
							top: '-5'
						},
						glasses: 'sunglasses'
					},
					personality: {
						fitness: 9,
						neediness: 8,
						speed: 10,
						stubbornness: 10,
						appetite: 10,
						sleepiness: 1
					}
				},
				{
					name: 'Ellie de Scheve', active: true, size: 1,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: ''
					},
					personality: {
						fitness: 4,
						neediness: 10,
						speed: 5,
						stubbornness: 6,
						appetite: 6,
						sleepiness: 7
					}
				},
				{
					name: 'TomTomTommie', active: true, size: 1,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: ''
					},
					personality: {
						fitness: 6,
						neediness: 10,
						speed: 8,
						stubbornness: 4,
						appetite: 9,
						sleepiness: 4
					}
				},
				{
					name: 'Pietertje', active: true, size: 1,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: ''
					},
					personality: {
						fitness: 4,
						neediness: 4,
						speed: 4,
						stubbornness: 2,
						appetite: 4,
						sleepiness: 3
					}
				},
				{
					name: 'SUPERAnna', active: true, size: 2,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: ''
					},
					personality: {
						fitness: 3,
						neediness: 1,
						speed: 3,
						stubbornness: 1,
						appetite: 2,
						sleepiness: 10
					}
				},
				{
					name: 'Jochem', active: true, size: 2,
					happiness: 100, hunger: 100, energy: 100,
					outfit: {
						hat: '',
						glasses: 'sunglasses'
					},
					personality: {
						fitness: 10,
						neediness: 10,
						speed: 3,
						stubbornness: 9,
						appetite: 8,
						sleepiness: 1
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
				}, this.delayCoefficient * (1000 + (11 - cow.personality.neediness) * 300))
				setInterval(() => {
					cow.hunger = Math.max(0, cow.hunger - 1)
				}, this.delayCoefficient * (1000 + (11 - cow.personality.appetite) * 400))
				setInterval(() => {
					cow.energy = Math.max(0, cow.energy-1)
				}, this.delayCoefficient * (1000 + cow.personality.fitness * 400))
			})
		},
		modifyStats(type, amount, name) {
			let affectedCows = this.cows
			if (name) {
				affectedCows = affectedCows.filter((cow) => { return cow.name == name })
			}
			affectedCows.forEach((cow) => {
				if (type == 'happiness') cow.happiness = Math.max(0, Math.min(100, cow.happiness + amount))
				else if (type == 'hunger') cow.hunger = Math.max(0, Math.min(100, cow.hunger + amount))
				else if (type == 'energy') cow.energy = Math.max(0, Math.min(100, cow.energy + amount))
			})
		}
	},
	computed: {
		delayCoefficient () {
			return 2 - this.appSpeed
		}
	},
	mounted () {
		this.startDepletions()
		this.cows.forEach((cow) => {
			cow.happiness = 80
			cow.hunger = 80
			cow.energy = 80
		})
	}
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 0px;
	height: 100%;
	width: 100%;
	position: absolute;
}
</style>
