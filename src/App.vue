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
</style>
