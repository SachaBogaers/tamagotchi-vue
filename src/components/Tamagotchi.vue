<template>
	<div @click="petCow"  
		class="tamagotchi" 
		ref="tamagotchi" 
		:style="{left: this.x + '%', top: this.y + '%'}">
		<div
			class="tamagotchi__display"
			:class="{'tamagotchi__display--flipped': direction=='right'}">
			<div 
				class="tamagotchi__display__cow"
				:class="{'tamagotchi__display__cow--walking': walking}">
				<img 
					class="tamagotchi__display__img" 
					:src="image"/>
				<img v-if="outfit.hat" class="outfit hat" :src="hatImage"/>
			</div>
		</div>
		<p>{{name}}</p>
		<p>Happiness: {{happiness}}</p>
		<p>Status: {{status}}</p>
	</div>
</template>

<script>
import { gsap } from 'gsap'

export default {
	props: {
		name: String,
		sleepiness: Number,
		speed: Number,
		neediness: Number,
		outfit: Object
	},
	data () {
		return {
			happiness: 20,
			status: 'neutral',
			// left: 50,
			// top: 50,
			x: 80*Math.random(),
			y: 80*Math.random(),
			direction: 'left',
			walking: false,
		}
	},
	watch: {
		status: function(newStatus) {
			if (newStatus == 'dead') {
				this.walking = false
			}
		}
	},
	methods: {
		petCow() {
			this.happiness = this.happiness + 5
			this.updateStatus()
		},
		// updateStats () {
		// 	this.happiness = Math.max(0, this.happiness - 1)

		// 	setTimeout(() => { this.updateStats() }, 1500)
		// },
		updateStatus () {
			if (this.happiness > 15) {
				this.status = 'superHappy'
			} else if (this.happiness > 7) {
				this.status = 'happy'
			} else if (this.hgappiness > 4) {
				this.status = 'neutral'
			} else if (this.happiness > 0) {
				this.status = 'sad'
			} else {
				this.status = 'dead'
			}
		},
		walk () {
			if (this.status != 'dead') {
				let x = this.x + (20*Math.random()- 10)
				x = Math.max(0, x)
				x = Math.min(80, x)
				let y = this.y + (20*Math.random()- 10)
				y = Math.max(0, y)
				y = Math.min(80, y)
				if (x > this.x) {
					this.direction = "right"
				} else {
					this.direction = "left"
				}
				// const walkDistance = Math.sqrt((this.x - x)^2 + (this.y - y)^2)
				this.walking = true
				this.x = x
				this.y = y
				setTimeout(() => this.walking = false, 2000)
			}
			setTimeout(this.walk, 5000*Math.random() + 2000)
		},
		walkGsap () {
		const { tamagotchi } = this.$refs
		gsap.to(tamagotchi, {repeat: -1, delay: this.sleepiness, duration: 11 - this.speed, left: "+=random(-20, 20)%" , top: '+=random(-20, 20)%', repeatRefresh: true})
		}
	},
	computed: {
		image () {
			let image_name = this.status + 'Cow'
			let image = require('../assets/' + image_name + '.png')
			return image
		},
		hatImage () {
			let image_name = this.outfit.hat
			let image = require('../assets/hats/' + image_name + '.png')
			return image
		}
	},
	mounted () {
		this.updateStatus()
		setInterval(() => {
			this.happiness = Math.max(0, this.happiness - 1)
			this.updateStatus()
		}, 1000 + (11 - this.neediness) * 300)
		// this.updateStats()
		setTimeout(this.walk, Math.random()*3000 + 2000)
	}
}
</script>

<style scoped>
.tamagotchi {
	position: absolute;
	transition: left 2s ease-in-out, top 2s ease-in-out;
	color: white;
	font-weight: 700;
}
	.tamagotchi__display {
		/* height: 300px;
		width: 500px; */
	}
		.tamagotchi__display--flipped {
			transform: scaleX(-1);
		}
	.tamagotchi__display__cow {
		animation: bounce 0.5s infinite alternate;
		position: relative;
	}
		.tamagotchi__display__cow .outfit{
			position: absolute;
		}
		.tamagotchi__display__cow .hat {
			top: -33.333%;
			left: -4.167%;
		}
		.tamagotchi__display__cow--walking {
			animation: bounce 0.1s infinite alternate;
		}
		.tamagotchi__display__img {
			display: block;
		}

@keyframes bounce {
	from {
		transform: translateY(0px);
	}
	to {
		transform: translateY(-3px);
	}
}

</style>
