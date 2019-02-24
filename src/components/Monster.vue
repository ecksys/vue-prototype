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
            // Listen for 'playerHasAttacked' emit trigger
            eventBus.$on('playerHasAttacked', (damage) => {
                // Adjust the monster's health
                this.health -= damage;

                // Roll for damage and emit 'monsterHasAttacked' trigger
                var roll = Math.max(Math.floor((Math.random() * this.maxAttack)) + 1, this.minAttack);
                eventBus.$emit('monsterHasAttacked', roll);

                // Emit 'printLog' trigger
                eventBus.$emit('printLog', { text: 'Monster did ' + roll + ' damage.' })
            });
        }
    }
</script>

<style lang="scss">
</style>
