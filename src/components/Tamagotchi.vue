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
				:class="{'tamagotchi__display__cow--jumping': jumping,
				'tamagotchi__display__cow--saltoing': saltoing}"
				:style="{'animation-duration': animationDuration + 's'}">
				<img
					class="tamagotchi__display__img" 
					:src="image"/>
				<img v-if="outfit.hat" class="outfit hat" :src="hatImage" :style="hatLocation"/>
				<img v-if="outfit.glasses" class="outfit glasses" :src="glassesImage"/>
			</div>
		</div>
		<p>{{name}}</p>
		<button @click="feedCow" type="button">Feed</button>
		<button @click="hugCow" type="button">Hug</button>
		<button @click="salto" type="button">Salto</button>
	</div>
</template>

<script>
import { gsap } from 'gsap'

export default {
	props: {
		name: String,
		sleepiness: Number,
		appetite: Number,
		stubbornness: Number,
		outfit: Object,
		happiness: Number,
		hunger: Number,
		energy: Number,
		personality: Object,
		items: Array
	},
	data () {
		return {
			// left: 50,
			// top: 50,
			x: 80*Math.random(),
			y: 80*Math.random(),
			direction: 'left',
			walking: false,
			jumping: false,
			saltoing: false,
			action: ''
		}
	},
	watch: {
		'status': function () {
			if (this.status == 'dead') {
				this.walking = false
			}
		},
		'energy': function () {
			if (this.energy == 0 && this.action != 'sleeping') {
				this.sleep()
			}
		},
		'hunger': function() {
			if (this.hunger <= 40 && !['sleeping', 'moving_to', 'eating'].includes(this.action)) {
				let apples = this.items.filter((item) => { return (item.name == 'apple' && !item.reserved) })
				if (apples.length == 0) return
				let apple = apples[Math.floor(Math.random() * apples.length)]
				let cow_height = 10 // In percentage of total heigh
				this.move(apple.left, apple.top - cow_height)
				this.action = 'moving_to'
				this.$emit('reserveItem', apple.id)
				let move_duration = 2
				setTimeout(() => this.eat(apple), move_duration * 1000)
			}
		}
	},
	methods: {
		petCow() {
			this.$emit('modifyStats', 'happiness', 5, this.name)
		},
		hugCow() {
			this.$emit('modifyStats', 'happiness', 10, this.name)
			this.jump(false)
		},
		feedCow() {
			this.$emit('modifyStats', 'hunger', 10, this.name)
		},
		eat (food) {
			if (this.action == 'moving_to') this.action = ''
			if (!['sleeping'].includes(this.action) && this.status != 'dead') {
				this.action = 'eating'
				this.direction = 'left'
				setTimeout(() => {
					this.action = ''
					this.$emit('modifyStats', 'hunger', food.foodValue, this.name)
					this.$emit('removeItem', food.id)
				}, 2000)
			}
		},
		walk () {
			if (this.status != 'dead'
			&& !this.saltoing
			&& !['sleeping', 'moving_to', 'eating'].includes(this.action)) {
				let range = 2*this.personality.speed + 5
				let x = this.x + (2 * range * Math.random() - range)
				x = Math.max(0, x)
				x = Math.min(80, x)
				let y = this.y + (2 * range * Math.random() - range)
				y = Math.max(0, y)
				y = Math.min(80, y)
				this.move(x, y)
			}
			setTimeout(this.walk, (this.sleepiness*1000)*Math.random() + 2000)
		},
		move (x, y) {
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
		},
		jump (costsEnergy=true) {
			if (!this.jumping && this.status != 'dead' && !['sleeping'].includes(this.action)) {
				if (this.listens()) {
					this.jumping = true
					if (costsEnergy) this.$emit('modifyStats', 'energy', -5, this.name)
					if (costsEnergy) this.$emit('modifyStats', 'hunger', -10, this.name)
					setTimeout(() => this.jumping = false, this.animationDuration * 1000)
				}
			}
		},
		salto () {
			if (!this.saltoing && !this.jumping && this.status != 'dead' && !['sleeping'].includes(this.action)) {
				if (this.listens()) {
					this.saltoing = true
					this.$emit('modifyStats', 'energy', -10, this.name)
					setTimeout(() => this.saltoing = false, this.animationDuration * 1000)
				}
			}
		},
		sleep () {
			this.action = 'sleeping'
			var sleepInterval = setInterval(() => {
				this.$emit('modifyStats', 'energy', 1, this.name)
				if (this.energy >= 80 || this.hunger <= 20) {
					this.action = ''
					clearInterval(sleepInterval)
				}
			}, 150 + this.sleepiness * 25)
		},
		walkGsap () {
		const { tamagotchi } = this.$refs
		gsap.to(tamagotchi, {repeat: -1, delay: this.sleepiness, duration: 11 - this.personality.speed, left: "+=random(-20, 20)%" , top: '+=random(-20, 20)%', repeatRefresh: true})
		},
		listens () {
			return Math.random() < this.listenProbability
		}
	},
	computed: {
		status () {
			let status = ""
			if (this.hunger ==  0) {
				status = 'dead'
			} else if (this.hunger < 15) {
				status = 'starving'
			} else if (this.hunger < 30) {
				status = 'grumpy'
			}
			else {
				if (this.happiness > 80) {
					status = 'superHappy'
				} else if (this.happiness > 50) {
					status = 'happy'
				} else if (this.happiness > 20) {
					status = 'neutral'
				} else if (this.happiness > 0) {
					status = 'sad'
				} else {
					status = 'superSad'
				}
			}
			return status
		},
		image () {
			let image_name = this.status
			if (this.status == 'dead') image_name = 'dead'
			else if (this.action == 'sleeping') image_name = 'sleeping'
			else if (this.action == 'eating') image_name = 'eating'
			image_name += 'Cow'
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
			if (this.action == 'sleeping')
				return animationDuration
			if (this.jumping) {
				animationDuration = 0.4
			} else if (this.saltoing) {
				animationDuration = 1
			} else if (this.action == 'eating') {
				animationDuration = 0.1
			} else {
				if (this.walking) animationDuration = this.status == 'sad' ? 0.2 : 0.1
				else if (!['dead', 'sad'].includes(this.status)) animationDuration = 0.5
			}
			return animationDuration
		},
		listenProbability () {
			let statPercentage = (this.happiness + this.hunger) / 200
			let listenProbability = (0.2 + 0.6 * statPercentage) + (0.8 - 0.6 * statPercentage) * (11 - this.stubbornness) / 10
			// Round to two decimals
			listenProbability = Math.round(listenProbability * 100) / 100
			return listenProbability
		}
	},
	mounted () {
		setTimeout(this.walk, Math.random()*3000 + 2000)
		this.$root.$on('jump', this.jump)
		this.$root.$on('salto', this.salto)
	}
}
</script>

<style scoped>
.tamagotchi {
	position: absolute;
	transition: left 2s ease-in-out, top 2s ease-in-out;
	color: white;
	font-weight: 700;
  text-align: center;
}
	.tamagotchi__display {
		position: relative;
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
			animation: jump;
		}
		.tamagotchi__display__cow--saltoing {
			animation: salto;
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
	50% {
		transform: translateY(-50px);
	}
	to {
		transform: translateY(0px);
	}
}

@keyframes salto {
	from {
		transform: translateY(0);
	}
	25% {
		transform: translateY(-100px) rotate(0deg);
	}
	100% {
		transform: translateY(0) rotate(-360deg);
	}
}

</style>
