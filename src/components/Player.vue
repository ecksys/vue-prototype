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
                // Roll for damage and emit 'playerHasAttacked' trigger to Monster component
                var roll = Math.max(Math.floor((Math.random() * this.maxAttack)) + 1, this.minAttack);
                eventBus.$emit('playerHasAttacked', roll);

                // Emit 'printLog' trigger to Log component
                eventBus.$emit('printLog', {
                    timestamp: 'P-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                    text: 'Player did ' + roll + ' damage.'
                });
            }
        },
        created() {
            // Listen for 'monsterHasAttacked' emit trigger from Monster component
            eventBus.$on('monsterHasAttacked', (damage) => {
                this.health -= damage; // Adjust the player's health

                // Check if player's health is equal or less than 0
                if(this.health <= 0) {
                    this.health = 0; // Health should never go below 0
                    eventBus.$emit('playerHasDied'); // Emit 'playerHasDied' trigger to parent App
                }
            });
        }
    }
</script>

<style lang="scss">
</style>
