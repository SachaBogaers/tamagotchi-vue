<template>
	<div @click="petCow" class="tamagotchi">
		<img :src="image_url"/>
		<p>{{name}}</p>
		<p>Happiness: {{happiness}}</p>
		<p>Mood: {{mood}}</p>
	</div>
</template>

<script>
export default {
	props: {
		name: String
	},
	data () {
		return {
			happiness: 10,
			mood: 'happy'
		}
	},
	methods: {
		petCow() {
			this.happiness = this.happiness + 5
			this.updateMood()
		},
		updateStats () {
			console.log('UPDATE')
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
		}
	},
	computed: {
		image_url () {
			let image_name = this.mood + 'Cow'
			var image = require('../assets/' + image_name + '.png')
			return image
		}
	},
	mounted () {
		this.updateStats()
	}
}
</script>

