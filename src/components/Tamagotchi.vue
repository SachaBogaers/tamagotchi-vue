<template>
	<div @click="petCow" class="tamagotchi" ref="tamagotchi" v-bind:style="styleObject">
		<img :src="image_url"/>
		<p>{{name}}</p>
		<p>Happiness: {{happiness}}</p>
		<p>Mood: {{mood}}</p>
	</div>
</template>

<script>
import { gsap } from 'gsap'

export default {
	props: {
		name: String
	},
	data () {
		return {
			happiness: 10,
			mood: 'happy',
			speed: 8,
			styleObject: {
				left: '50%',
				top: '50%'
			}
		}
	},
	methods: {
		petCow() {
			this.happiness = this.happiness + 5
			this.updateMood()
		},
		updateStats () {
			this.happiness = Math.max(0, this.happiness - 1)
			this.updateMood()
			setTimeout(() => { this.updateStats() }, 1500)
		},
		updateMood () {
			if (this.happiness > 15) {
				this.mood = 'superHappy'
			} else if (this.happiness > 7) {
				this.mood = 'happy'
			} else if (this.happiness > 4) {
				this.mood = 'neutral'
			} else if (this.happiness > 0) {
				this.mood = 'sad'
			} else {
				this.mood = 'dead'
			}
		},
		walk () {
		const { tamagotchi } = this.$refs
		const tl = gsap.timeline({
		})
		console.log(this.name)
		tl.to(tamagotchi, 3, {left: "random(0, 100)%", top: 'random(0, 100)%'})
			.to(tamagotchi, 3, {left: "random(0, 100)%", top: "random(0, 100)%"}, "+=5")
			.to(tamagotchi, 3, {left: "random(0, 100)%", top: "random(0, 100)%"})
		}
	},
	computed: {
		image_url () {
			let image_name = this.mood + 'Cow'
			var image = require('../assets/' + image_name + '.png')
			return image
		},
	},
	mounted () {
		this.updateStats()
		this.walk()
	}
}
</script>

<style scoped>
.tamagotchi {
	position: absolute;
}


</style>
