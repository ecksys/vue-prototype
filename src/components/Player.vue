<template>
    <div class="col">
        <h2>Player</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: health + '%' }">{{ health }}</div>
        </div>
        <button @click="playerAttack">Attack</button>
        <button>Special</button>
    </div>
</template>

<script>
    import { eventBus } from '../main';

    export default {
        data() {
            return {
                health: 100,
                minAttack: 1,
                maxAttack: 4
            }
        },
        methods: {
            playerAttack() {
                // Roll for damage and emit 'playerHasAttacked' trigger
                var roll = Math.max(Math.floor((Math.random() * this.maxAttack)) + 1, this.minAttack);
                eventBus.$emit('playerHasAttacked', roll);

                // Emit 'printLog' trigger
                eventBus.$emit('printLog', { text: 'Player did ' + roll + ' damage.' })
            }
        },
        created() {
            // Listen for 'monsterHasAttacked' emit trigger
            eventBus.$on('monsterHasAttacked', (damage) => {
                // Adjust the player's health
                this.health -= damage;
            })
        }
    }
</script>

<style lang="scss">
</style>
