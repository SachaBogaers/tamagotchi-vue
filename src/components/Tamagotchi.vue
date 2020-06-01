<template>
	<div  
		class="tamagotchi" 
		ref="tamagotchi" 
		:style="{left: this.x + '%', top: this.y + '%'}">
		<div
			class="tamagotchi__display"
			:class="{'tamagotchi__display--flipped': direction=='right'}">
			<div
				@click="petCow"  
				class="tamagotchi__display__cow"
				:class="{'tamagotchi__display__cow--jumping': jumping}"
				:style="{'animation-duration': animationDuration + 's'}">
				<img
					class="tamagotchi__display__img" 
					:src="image"/>
				<img v-if="outfit.hat" class="outfit hat" :src="hatImage" :style="hatLocation"/>
				<img v-if="outfit.glasses" class="outfit glasses" :src="glassesImage"/>
			</div>
		</div>
		<p>{{name}}</p>
		<p>Happiness: {{happiness}}</p>
		<p>Status: {{status}}</p>
		<p>Hunger: {{hunger}}</p>
		<button @click="feedCow" type="button">Feed</button>
		<button @click="hugCow" type="button">Hug</button>
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
		appetite: Number,
		outfit: Object,
		happiness: Number,
		hunger: Number
	},
	data () {
		return {
			// left: 50,
			// top: 50,
			x: 80*Math.random(),
			y: 80*Math.random(),
			direction: 'left',
			walking: false,
			jumping: false
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
			this.$emit('modifyStats', 'happiness', 5, this.name)
		},
		hugCow() {
			this.$emit('modifyStats', 'happiness', 10, this.name)
			this.jump()
		},
		feedCow() {
			this.$emit('modifyStats', 'hunger', 10, this.name)
		},
		walk () {
			if (this.status != 'dead') {
				let range = 2*this.speed + 5
				let x = this.x + (2 * range * Math.random() - range)
				x = Math.max(0, x)
				x = Math.min(80, x)
				let y = this.y + (2 * range * Math.random() - range)
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
			setTimeout(this.walk, (this.sleepiness*1000)*Math.random() + 2000)
		},
		jump () {
			if (!this.jumping && this.status != 'dead') {
				this.jumping = true
				setTimeout(() => this.jumping = false, 2 * this.animationDuration * 1000)
			}
		},
		walkGsap () {
		const { tamagotchi } = this.$refs
		gsap.to(tamagotchi, {repeat: -1, delay: this.sleepiness, duration: 11 - this.speed, left: "+=random(-20, 20)%" , top: '+=random(-20, 20)%', repeatRefresh: true})
		}
	},
	computed: {
		status () {
			let status = ""
			if (this.hunger ==  0) {
				status = 'dead'
			} else {
				if (this.happiness > 80) {
					status = 'superHappy'
				} else if (this.happiness > 50) {
					status = 'happy'
				} else if (this.happiness > 20) {
					status = 'neutral'
				} else {
					status = 'sad'
				} 
			}
			return status
		},
		image () {
			let image_name = this.status + 'Cow'
			let image = require('../assets/' + image_name + '.png')
			return image
		},
		hatImage () {
			let image_name = this.outfit.hat.name
			let image = require('../assets/hats/' + image_name + '.png')
			return image
		},
		hatLocation () {
			let hatLocation = {
				left: 'calc(100% * (' + this.outfit.hat.left + '/24))',
				top: 'calc(100% * (' + this.outfit.hat.top + '/15))'
			}
			return hatLocation
		},
		glassesImage () {
			let image_name = this.outfit.glasses
			let image = require('../assets/glasses/' + image_name + '.png')
			return image
		},
		animationDuration () {
			let animationDuration = 0
			if (this.jumping) {
				animationDuration = 0.2
			} else {
				if (this.walking) animationDuration = this.status == 'sad' ? 0.2 : 0.1
				else if (!['dead', 'sad'].includes(this.status)) animationDuration = 0.5
			}
			return animationDuration
		}
	},
	mounted () {
		setTimeout(this.walk, Math.random()*3000 + 2000)
		this.$root.$on('jump', this.jump)
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
		animation: bounce infinite alternate;
		position: relative;
	}
		.tamagotchi__display__cow .outfit{
			position: absolute;
		}
		.tamagotchi__display__cow .glasses {
			top: calc(100% * (4 / 15));
			left: calc(100% * (3 / 24));
		}
		.tamagotchi__display__cow--jumping {
			animation: jump 1s 2 alternate;
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

@keyframes jump {
	from {
		transform: translateY(0px);
	}
	to {
		transform: translateY(-50px);
	}
}

</style>
