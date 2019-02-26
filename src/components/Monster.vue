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
                minDmg: 2,
                maxDmg: 5
            }
        },
        methods: {
            // Method to reset the Monster component
            newMonster() {
                this.health = 100;
                this.minDmg = 2;
                this.maxDmg = 5;
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
                    this.newMonster(); // Reset the Monster component
                }
                else {
                    // Roll for damage and emit 'monsterHasAttacked' trigger to Player component
                    const roll = Math.max(Math.floor((Math.random() * this.maxDmg) + 1), this.minDmg);
                    eventBus.$emit('monsterHasAttacked', roll);
                }
            }),
            // Listen for 'resetEverything' emit trigger from App
            eventBus.$on('resetEverything', () => {
                this.newMonster();
            })
        }
    }
</script>

<style lang="scss">
</style>
