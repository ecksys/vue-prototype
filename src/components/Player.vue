<template>
    <div class="col interface__panel">
        <h2>Player</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: health + '%' }">{{ health }}</div>
        </div>
        <button @click="playerAttack">Attack</button>
        <button disabled>Special</button>
    </div>
</template>

<script>
    import { eventBus } from '../main';

    export default {
        data() {
            return {
                health: 100,
                minDmg: 1,
                maxDmg: 1,
                armorRating: 0
            }
        },
        methods: {
            playerAttack() {
                // Roll for damage and emit 'playerHasAttacked' trigger to Monster component
                const roll = Math.max(Math.floor((Math.random() * this.maxDmg) + 1), this.minDmg);
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
                (damage - this.armorRating) < 0 ? damage = 0 : damage -= this.armorRating; // Adjust the damage after applying armor rating
                this.health -= damage; // Adjust the player's health

                // Check if player's health is equal or less than 0
                if(this.health <= 0) {
                    this.health = 0; // Health should never go below 0
                    eventBus.$emit('playerHasDied'); // Emit 'playerHasDied' trigger to parent App
                }
                else {
                    if(damage == 0) {
                        // Emit 'printLog' trigger to Log component
                        eventBus.$emit('printLog', {
                            timestamp: 'M-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                            text: 'You deflected the monster\'s attack!'
                        });
                    }
                    else {
                        // Emit 'printLog' trigger to Log component
                        eventBus.$emit('printLog', {
                            timestamp: 'M-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                            text: 'Monster did ' + damage + ' damage.'
                        });
                    }
                }
            }),
            // Listen for 'updatePlayerDamage' emit trigger from Upgrade component
            eventBus.$on('updatePlayerDamage', (weapon) => {
                this.minDmg = weapon.minDmg;
                this.maxDmg = weapon.maxDmg;
            }),
            // Listen for 'updatePlayerArmor' emit trigger from Upgrade component
            eventBus.$on('updatePlayerArmor', (armor) => {
                this.armorRating = armor.rating;
            }),
            // Listen for 'resetEverything' emit trigger from App
            eventBus.$on('resetEverything', () => {
                this.health = 100;
                this.minDmg = 1;
                this.maxDmg = 1;
                this.armorRating = 0;
            })
        }
    }
</script>

<style lang="scss" scoped>
    .progress {
        margin-bottom: 10px;
    }
</style>
