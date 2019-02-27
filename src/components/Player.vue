<template>
    <div class="col interface__panel">
        <h2>Player</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: ((health/totalHealth) * 100) + '%' }">{{ health }}</div>
        </div>
        <button @click="attack">Attack</button>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    export default {
        data() {
            return {
                health: 100,
                totalHealth: 100,
                minDmg: 1,
                maxDmg: 1,
                armor: 0
            }
        },
        methods: {
            reset() {
                this.health = 100;
                this.totalHealth = 100;
                this.minDmg = 1;
                this.maxDmg = 1;
                this.armor = 0;
            },
            attack() {
                // Roll for damage and emit to the monster component
                const roll = Math.floor(Math.random() * (this.maxDmg - this.minDmg + 1)) + this.minDmg;
                this.log('You hit the monster for ' + roll + ' damage.');
                eventBus.$emit('playerAttack', roll);
            },
            log(text) {
                // Send a text message to the log component
                eventBus.$emit('updateLog', text);
            }
        },
        created() {
            eventBus.$on('monsterAttack', (damage) => {
                // Adjust the damage based on the armor rating before taking damage
                (damage - this.armor) < 0 ? damage = 0 : damage -= this.armor;
                this.health -= damage;

                if(this.health <= 0) {
                    this.health = 0;
                }
                else {
                    // If no damage is dealt, print the appropriate log
                    if(damage == 0) this.log('You deflected the monster\'s attack!')
                    else this.log('The monster hits you for ' + damage + ' damage.');
                }
            }),
            eventBus.$on('upgradePlayer', (obj) => {
                // Update this component with the new details
                if(obj.type == 'weapon') {
                    this.minDmg = obj.item.minDmg;
                    this.maxDmg = obj.item.maxDmg;
                }
                else {
                    this.armor = obj.item.rating;
                }
            })
        }
    }
</script>

<style lang="scss" scoped>
    .progress {
        margin-bottom: 10px;
    }
</style>
