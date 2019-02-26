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
			<div class="row">
				<div class="col">
					<button @click="newGame">New Game</button>
				</div>
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
		methods: {
			newGame() {
				// Emit 'resetEverything' trigger to all components
				eventBus.$emit('resetEverything');
			}
		},
		created() {
			// Listen for 'playerHasDied' emit trigger from Player component
			eventBus.$on('playerHasDied', () => {
				// Emit 'printLog' trigger to Log component
				eventBus.$emit('printLog', {
					timestamp: Date.now(), // Timestamp used to generate key for the v-bind:key
					text: 'You died!'
				});
			}),
			// Listen for 'monsterHasDied' emit trigger from Monster component
			eventBus.$on('monsterHasDied', () => {
				// Emit 'printLog' trigger to Log component
				eventBus.$emit('printLog', {
					timestamp: Date.now(), // Timestamp used to generate key for the v-bind:key
					text: 'You killed the monster!'
				});
			})
		}
	}
</script>

<style lang="scss">
</style>
