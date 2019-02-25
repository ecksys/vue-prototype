<template>
    <div class="col">
        <h2>Monster</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: health + '%' }">{{ health }}</div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from '../main';

    export default {
        data() {
            return {
                health: 100,
                minAttack: 2,
                maxAttack: 5
            }
        },
        created() {
            // Listen for 'playerHasAttacked' emit trigger from Player component
            eventBus.$on('playerHasAttacked', (damage) => {
                this.health -= damage; // Adjust the monster's health

                // Check if player's health is equal or less than 0
                if(this.health <= 0) {
                    this.health = 0; // Health should never go below 0
                    eventBus.$emit('monsterHasDied'); // Emit 'playerHasDied' trigger to parent App
                }
                else {
                    // Roll for damage and emit 'monsterHasAttacked' trigger to Player component
                    const roll = Math.max(Math.floor((Math.random() * this.maxAttack)) + 1, this.minAttack);
                    eventBus.$emit('monsterHasAttacked', roll);
    
                    // Emit 'printLog' trigger to Log component
                    eventBus.$emit('printLog', {
                        timestamp: 'M-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                        text: 'Monster did ' + roll + ' damage.'
                    });
                }
            })
        }
    }
</script>

<style lang="scss">
</style>
