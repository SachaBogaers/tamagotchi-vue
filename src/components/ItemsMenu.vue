<template>
  <div id="itemsMenu">
		<p>ITEMS</p>
		<div v-for="itemClass in items" :key="itemClass.class">
			<p>{{itemClass.class}}</p>
			<div @click="purchase(item)" v-for="item in itemClass.items" :key="item.name" class="item">
				<img :src="image(item.class, item.name)" class="item__image">
				{{item.fullName}}
				{{item.cost}}
			</div>
		</div>
  </div>
</template>

<script>


export default {
  name: 'ItemsMenu',
  components: {
		
	},
	props: {
		money: Number
	},
	data () {
		return {
			items: [
				{
					'class': 'Food',
					'items': [
						{
							name: 'apple',
							fullName: 'Apple',
							class: 'food',
							foodValue: 15,
							cost: 5
						},
						{
							name: 'melon',
							fullName: 'Melon',
							class: 'food',
							foodValue: 25,
							cost: 10
						},
						{
							name: 'pizza',
							fullName: 'Pizza',
							class: 'food',
							foodValue: 50,
							happinessValue: 15,
							cost: 20
						},
						{
							name: 'lemon',
							fullName: 'Lemon',
							class: 'food',
							foodValue: 3,
							happinessValue: -4,
							cost: 2
						},
						{
							name: 'donut',
							fullName: 'Donut',
							class: 'food',
							foodValue: 14,
							happinessValue: 10,
							cost: 5
						},
					]
				},
				{
					'class': 'Hats',
					'items': [
						{
							name: 'cowboy',
							fullName: 'Cowboy Hat',
							class: 'hat',
							cost: 1,
							left: -1,
							top: -5
						},
						{
							name: 'santa',
							fullName: 'Santa Hat',
							class: 'hat',
							cost: 1,
							left: 1,
							top: -11
						},
					]
				},
				{
					'class': 'Glasses',
					'items': [
						{
							name: 'sunglasses',
							fullName: 'Sunglasses',
							class: 'glasses',
							cost: 10,
							left: 3,
							top: 4
						},
						{
							name: 'faceMask',
							fullName: 'Face mask',
							class: 'glasses',
							cost: 1,
							left: 3,
							top: 2
						},
						{
							name: 'pinkGlasses',
							fullName: 'Pink glasses',
							class: 'glasses',
							cost: 1,
							left: 3,
							top: 4
						}
					],
				},
				{
					'class': 'Shirts',
					'items': [
						{
							name: 'tutu',
							fullName: 'Tutu',
							class: 'shirt',
							cost: 1,
							left: 6,
							top: 2
						},
						{
							name: 'colouredSweater',
							fullName: 'Coloured Sweater',
							class: 'shirt',
							cost: 1,
							left: 6,
							top: 2
						},
						{
							name: 'superCow',
							fullName: 'Super Cow',
							class: 'shirt',
							cost: 1,
							left: 6,
							top: 0
						},
					]
				},
				{
					'class': 'Gifts',
					'items': [
						{
							name: 'gift1',
							fullName: 'Mystery gift',
							class: 'gift',
							cost: 1,
							happinessValue: 10
						},
						{
							name: 'bowlingPin',
							fullName: 'Bowling pin',
							class: 'gift',
							cost: 1,
							happinessValue: 10
						},						{
							name: 'controllerP',
							fullName: 'Playstation Controller',
							class: 'gift',
							cost: 1,
							happinessValue: 10
						},
					]
				},
			]
		}
	},
	methods: {
		image(itemClass, itemName) {
			let image = require('@/assets/' + itemClass + '/' + itemName +  '.png')
			return image
		},
		purchase(item) {
			if (this.money < item.cost) alert('U HAVE NO MONNIE')
			else {
				this.$emit('spend', item.cost)
				if (item.class == 'food' || item.class == 'gift') {
					this.$root.$emit('placeItem', item)
				} else if (['hat', 'glasses', 'shirt'].includes(item.class)) {
					this.$root.$emit('giveOutfit', item)
				}
			}
		}
	},
	computed: {
	}
}
</script>

<style scoped>
#itemsMenu {
	padding: 10px;
	text-align: left;
	position: absolute;
	top: -250px;
	right: 15%;
	background-color: rgba(245, 245, 220, 0.9);
	width: 250px;
	height: 250px;
	color: black;
	font-weight: 1000;
	overflow-y: scroll;
}
	.item {
		cursor: pointer;
		margin-bottom: 10px;
	}
		.item__image {
			width: 30px;
		}

</style>
