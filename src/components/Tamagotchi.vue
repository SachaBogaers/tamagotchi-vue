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
				:class="{
					'tamagotchi__display__cow--jumping': jumping,
					'tamagotchi__display__cow--somersaulting': somersaulting,
					'tamagotchi__display__cow--dead': status == 'dead'
				}"
				:style="{'animation-duration': animationDuration + 's'}">
				<img
					class="tamagotchi__display__base" 
					:src="baseImage"/>
				<img v-if="outfit.shirt" class="outfit shirt" :src="outfitImage(outfit.shirt)" :style="outfitLocation(outfit.shirt)"/>
				<div class="tamagotchi__display__head" :style="headLocation">
					<img :src="headImage" class="tamagotchi__display__head__base"/>
					<img v-if="outfit.glasses" class="outfit glasses" :src="outfitImage(outfit.glasses)" :style="outfitLocation(outfit.glasses)"/>
					<img v-if="outfit.hat" class="outfit hat" :src="outfitImage(outfit.hat)" :style="outfitLocation(outfit.hat)"/>
				</div>
			</div>
		</div>
		<p>{{name}}</p>
		<button @click="feedCow" type="button">Feed</button>
		<button @click="hugCow" type="button">Hug</button>
		<button @click="somersault" type="button">Somersault</button>
		<button @click="strip" type="button">Strip</button>
	</div>
</template>

<script>
import { gsap } from 'gsap'
require("@/assets/hat/cowboy.png")

export default {
	props: {
		name: String,
		outfit: Object,
		happiness: Number,
		hunger: Number,
		energy: Number,
		personality: Object,
		items: Array
	},
	data () {
		return {
			x: 80*Math.random(),
			y: 80*Math.random(),
			direction: 'left',
			walking: false,
			jumping: false,
			somersaulting: false,
			action: ''
		}
	},
	watch: {
		// make sure cow stops walking when dead
		'status': function () {
			if (this.status == 'dead') {
				this.walking = false
			}
		},
		// watch cow's energy
		'energy': function () {
			// make cow sleep when energy reaches 0
			if (this.energy == 0 && this.action != 'sleeping' && this.status != 'dead') {
				this.sleep()
			}
		},
		// watch cow's hunger
		'hunger': function() {
			// avoid that cow starts this function when sleeping or when already moving or eating
			if (this.hunger <= 40 && !['sleeping', 'moving_to', 'eating'].includes(this.action)) {
				// filter apples out of list of items
				let foodItems = this.items.filter((item) => { return (item.class == 'food' && !item.reserved) })
				// no food means no eating
				if (foodItems.length == 0) return
				// select random apple
				let foodItem = foodItems[Math.floor(Math.random() * foodItems.length)]
				let cow_height = 10 // In percentage of total height
				// move to selected apple
				this.move(foodItem.left, foodItem.top - cow_height)
				this.action = 'moving_to'
				// make sure item gets reserved so other cows don't walk towards it
				this.$emit('reserveItem', foodItem.id)
				let move_duration = 2
				// call eat function once cow has reached destination
				setTimeout(() => this.eat(foodItem), move_duration * 1000)
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
			} else {
				this.$emit('reserveItem', food.id, false)
			}
		},
		walk () {
			if (this.status != 'dead'
			&& !this.somersaulting
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
			setTimeout(this.walk, (this.personality.sleepiness*1000)*Math.random() + 2000)
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
					let jumpEnergy = 5
					let jumpHunger = 10
					if (costsEnergy && this.hunger <= jumpHunger) return
					this.jumping = true
					if (costsEnergy) this.$emit('modifyStats', 'energy', -jumpEnergy, this.name)
					if (costsEnergy) this.$emit('modifyStats', 'hunger', -jumpHunger, this.name)
					setTimeout(() => this.jumping = false, this.animationDuration * 1000)
				}
			}
		},
		somersault () {
			if (!this.somersaulting && !this.jumping && this.status != 'dead' && !['sleeping'].includes(this.action)) {
				if (this.listens()) {
					this.somersaulting = true
					this.$emit('modifyStats', 'energy', -10, this.name)
					setTimeout(() => this.somersaulting = false, this.animationDuration * 1000)
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
			}, 150 + this.personality.sleepiness * 25)
		},
		strip () {
			this.$root.$emit('strip', this.name)
		},
		walkGsap () {
		const { tamagotchi } = this.$refs
		gsap.to(tamagotchi, {repeat: -1, delay: this.personality.sleepiness, duration: 11 - this.personality.speed, left: "+=random(-20, 20)%" , top: '+=random(-20, 20)%', repeatRefresh: true})
		},
		listens () {
			return Math.random() < this.listenProbability
		},
		outfitLocation (item) {
			let leftPixels = this.outfit[item.class].left
			let topPixels = this.outfit[item.class].top
			if (item.class == 'shirt' && this.action == 'sleeping') topPixels += 2
			let outfitLocation = {
				left: 'calc(100% * (' + leftPixels + '/24))',
				top: 'calc(100% * (' + topPixels + '/15))'
			}
			return outfitLocation
		},
		outfitImage (item) {
			let imageName = this.outfit[item.class].name
			let image = require('../assets/' + item.class + '/' + imageName + '.png')
			return image
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
		baseImage () {
			let image_name = 'standard'
			if (this.action == 'sleeping') image_name = 'sleeping'
			image_name += 'Base'
			let image = require('../assets/' + image_name + '.png')
			return image
		},
		headImage () {
			let image_name = this.status
			if (this.status == 'dead') image_name = 'dead'
			else if (this.action == 'sleeping') image_name = 'sleeping'
			image_name += 'Head'
			let image = require('../assets/' + image_name + '.png')
			return image
		},
		headLocation () {
			let left, top = 0
			if (this.action == 'sleeping') top = 'calc(100% * (2/15))'
			else if (this.action == 'eating') top = 'calc(100% * (3/15))'
			return { left, top }
		},
		animationDuration () {
			let animationDuration = 0
			if (this.action == 'sleeping')
				return animationDuration
			if (this.jumping) {
				animationDuration = 0.4
			} else if (this.somersaulting) {
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
			let listenProbability = (0.2 + 0.6 * statPercentage) + (0.8 - 0.6 * statPercentage) * (11 - this.personality.stubbornness) / 10
			// Round to two decimals
			listenProbability = Math.round(listenProbability * 100) / 100
			return listenProbability
		}
	},
	mounted () {
		setTimeout(this.walk, Math.random()*3000 + 2000)
		this.$root.$on('jump', this.jump)
		this.$root.$on('somersault', this.somersault)
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
		.tamagotchi__display__cow--dead {
			transform: scaleY(-1);
		}
		.tamagotchi__display__cow .outfit{
			position: absolute;
		}
		.tamagotchi__display__cow--jumping {
			animation: jump;
		}
		.tamagotchi__display__cow--somersaulting {
			animation: somersault;
		}
		.tamagotchi__display__base {
			display: block;
		}
		.tamagotchi__display__head {
			position: absolute;
			display: block;
		}
			.tamagotchi__display__head__base {
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

@keyframes somersault {
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
