<template>
	<div id="app">
		<div class="container">
			<div class="row">
				<div class="col">
					<header>
						<h1 class="text-center">An Untitled Vue Game</h1>
					</header>
				</div>
			</div>
			<div class="row interface">
				<div class="col">
					<div class="row">
						<app-player></app-player>
						<app-monster></app-monster>
					</div>
					<div class="row">
						<app-upgrades></app-upgrades>
						<app-log></app-log>
					</div>
				</div>
			</div>
			<div class="row">
				<div class="col">
					<footer class="text-right">
						<h6 class="mb-0">verson 1.1 - Made by Nate Cher</h6>
					</footer>
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

		&:disabled {
			opacity: 0.5;
		}
	}

	.interface {
		margin-bottom: 20px;
		border: 1px solid rgba(0, 0, 0, 0.5);

		&__panel {
			padding-top: 10px;
			padding-bottom: 10px;
			border: 1px solid rgba(0, 0, 0, 0.5);
		}
	}
</style>
