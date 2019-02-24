<template>
    <div class="col">
        <h2>Player</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: health + '%' }">{{ health }}</div>
        </div>
        <button @click="playerAttack">Attack</button>
        <button>Special</button>
        <p>Current Attack: {{ attack }}</p>
    </div>
</template>

<script>
    import { eventBus } from '../main';

    export default {
        data() {
            return {
                health: 100,
                attack: 1
            }
        },
        methods: {
            playerAttack() {
                eventBus.$emit('playerHasAttacked', this.attack);
            }
        },
        created() {
            eventBus.$on('monsterHasAttacked', (damage) => {
                this.health -= damage;
            })
        }
    }
</script>

<style lang="scss">
</style>
