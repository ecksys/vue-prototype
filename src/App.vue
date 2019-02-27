<template>
	<div id="app">
		<div class="container">
			<div class="row">
				<div class="col">
					<header>
						<h1 class="mt-4 text-center">An Untitled Vue Game</h1>
					</header>
				</div>
			</div>
			<div class="row interface">
				<div class="col">
					<div class="row">
						<app-player :isPlayerAlive="isPlayerAlive"></app-player>
						<app-monster></app-monster>
					</div>
					<div class="row">
						<app-upgrades :isPlayerAlive="isPlayerAlive"></app-upgrades>
						<app-log></app-log>
					</div>
				</div>
			</div>
			<footer>
				<div class="row align-items-center">
					<div class="col">
						<button @click="newGame">New Game</button>
					</div>
					<div class="col text-right">
						<h6 class="mb-0">v1.1 - Made by Nate Cher - <a target="blank" href="https://github.com/ecksys/vue-prototype">GitHub</a></h6>
					</div>
				</div>
			</footer>
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
		data() {
			return {
				isPlayerAlive: true
			}
		},
		components: {
			'AppPlayer': Player,
			'AppMonster': Monster,
			'AppLog': Log,
			'AppUpgrades': Upgrades
		},
		methods: {
			log(text, type = 'system') {
                // Send a text message to the log component
                const logObj = { text: text, type: type }
                eventBus.$emit('updateLog', logObj);
            },
			newGame() {
				// Resurrect the player and reset the game state
				this.isPlayerAlive = true;
				eventBus.$emit('resetTheGame');
			}
		},
		created() {
			eventBus.$on('playerHasDied', () => {
				// Kill the player and print the log
				this.isPlayerAlive = false;
				this.log('You died! Click on \'New Game\' to try again.');
			})
		}
	}
</script>

<style lang="scss">
	header {
		margin-bottom: 20px;
	}

	button {
		padding: 5px 20px;
		border: 0;
		border-radius: 30px;
		transition: 0.1s all ease-in-out;

		&:disabled {
			opacity: 0.5;
			cursor: not-allowed;
		}

		&:focus {
			outline: 0;
		}

		&:hover:not([disabled]) {
			background-color: #09f;
			color: #fff;
		}
	}

	.interface {
		min-height: 300px;
		margin-bottom: 20px;
		border: 1px solid rgba(0, 0, 0, 0.5);

		&__panel {
			padding-top: 10px;
			padding-bottom: 10px;
			border: 1px solid rgba(0, 0, 0, 0.5);
		}
	}
</style>
