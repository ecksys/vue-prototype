<template>
	<div id="app">
		<div class="container">
			<div class="row">
				<app-player></app-player>
				<app-monster></app-monster>
			</div>
			<div class="row">
				<app-log></app-log>
			</div>
			<div class="row">
				<app-upgrades></app-upgrades>
			</div>
		</div>
	</div>
</template>

<script>
	import { eventBus } from './main';
	import Player from './components/Player.vue';
	import Monster from './components/Monster.vue';
	import Log from './components/Log.vue';
	import Upgrades from './components/Upgrades.vue';

	export default {
		components: {
			'AppPlayer': Player,
			'AppMonster': Monster,
			'AppLog': Log,
			'AppUpgrades': Upgrades
		},
		created() {
			// Listen for 'playerHasDied' emit trigger from Player component
			eventBus.$on('playerHasDied', () => {
				alert('You died!');
			}),
			// Listen for 'monsterHasDied' emit trigger from Monster component
			eventBus.$on('monsterHasDied', () => {
				alert('The monster is dead!');
			})
		}
	}
</script>

<style lang="scss">
</style>
