<template>
    <div class="col interface__panel">
        <h2>Player</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: ((health/totalHealth) * 100) + '%' }">{{ health }}</div>
        </div>
        <button @click="attack" :disabled="!isPlayerAlive">Attack</button>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    export default {
        props: ['isPlayerAlive'],
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
            log(text, type = 'player') {
                // Send a text message to the log component
                const logObj = { text: text, type: type }
                eventBus.$emit('updateLog', logObj);
            },
            attack() {
                // Roll for damage and emit to the monster component
                const roll = Math.floor(Math.random() * (this.maxDmg - this.minDmg + 1)) + this.minDmg;
                this.log('You hit the monster for ' + roll + ' damage.');
                eventBus.$emit('playerAttack', roll);
            }
        },
        created() {
            eventBus.$on('resetTheGame', () => {
                this.reset();
            }),
            eventBus.$on('monsterAttack', (damage) => {
                // Adjust the damage based on the armor rating before taking damage
                (damage - this.armor) < 0 ? damage = 0 : damage -= this.armor;
                this.health -= damage;

                if(this.health <= 0) {
                    // Set the health to 0 and let the app know that player has died
                    this.health = 0;
                    eventBus.$emit('playerHasDied');
                }
                else {
                    // If no damage is dealt, print the appropriate log
                    if(damage == 0) this.log('You deflected the monster\'s attack!', 'monster')
                    else this.log('The monster hits you for ' + damage + ' damage.', 'monster');
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
