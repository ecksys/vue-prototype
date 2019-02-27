<template>
    <div class="col-sm-12 col-md-6 interface__panel">
        <h2>Player</h2>
        <div class="progress">
            <div class="progress-bar" :class="{ 'bg-success': !isCritical, 'bg-danger': isCritical }" :style="{ width: ((health/totalHealth) * 100) + '%' }">{{ health }}</div>
        </div>
        <div class="progress">
            <div class="progress-bar" :style="{ width: ((mana/totalMana) * 100) + '%' }">{{ mana }}</div>
        </div>
        <button @click="actionAttack" class="btn btn-danger" :disabled="!isPlayerAlive">Attack</button>
        <button @click="actionHeal" class="btn btn-primary" :disabled="!isPlayerAlive">Heal</button>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    export default {
        props: ['isPlayerAlive'],
        data() {
            return {
                isCritical: false,
                health: 100,
                totalHealth: 100,
                mana: 75,
                totalMana: 75,
                minDmg: 1,
                maxDmg: 1,
                armorRating: 0,
                healRating: 1,
                healCost: 10,
                manaRecovery: 3
            }
        },
        methods: {
            reset() {
                this.isCritical = false;
                this.health = 100;
                this.totalHealth = 100;
                this.mana = 75;
                this.totalMana = 75;
                this.minDmg = 1;
                this.maxDmg = 1;
                this.armorRating = 0;
                this.healRating = 1;
                this.healCost = 10;
                this.manaRecovery = 3;
            },
            log(text, type = 'player') {
                // Send a text message to the log component
                const logObj = { text: text, type: type }
                eventBus.$emit('updateLog', logObj);
            },
            actionAttack() {
                // Recover some mana whenever player attacks
                if(this.mana < this.totalMana)
                    (this.mana + this.manaRecovery) > this.totalMana ? this.mana = this.totalMana : this.mana += this.manaRecovery;

                // Roll for damage and emit to the monster component
                const roll = Math.floor(Math.random() * (this.maxDmg - this.minDmg + 1)) + this.minDmg;
                this.log('You hit the monster for ' + roll + ' damage.');
                eventBus.$emit('playerAttack', roll);
            },
            actionHeal() {
                if(this.mana - this.healCost >= 0) {
                    // Spend the mana for healing
                    this.mana -= this.healCost;

                    // Roll for healing, then let monster know to attack
                    const heal = Math.floor(this.totalHealth * (this.healRating / 100));
                    (this.health + heal > this.totalHealth) ? this.health = this.totalHealth : this.health += heal;
                    this.log('You healed ' + heal + ' hit points.');
                    eventBus.$emit('playerAttack', 0);
                }
                else {
                    this.log('You don\'t have enough mana!');
                }
            }
        },
        created() {
            eventBus.$on('resetTheGame', () => {
                this.reset();
            }),
            eventBus.$on('monsterAttack', (damage) => {
                // Adjust the damage based on the armor rating before taking damage
                (damage - this.armorRating) < 0 ? damage = 0 : damage -= this.armorRating;
                this.health -= damage;

                // Set isCritical to true if health is less than 25%
                Math.floor((this.health/this.totalHealth * 100) < 25) ? this.isCritical = true : this.isCritical = false;

                if(this.health <= 0) {
                    // Set the health to 0 and let the app know that player has died
                    this.health = 0;
                    eventBus.$emit('playerHasDied');
                }
                else {
                    // If no damage is dealt, print the appropriate log
                    if(damage == 0) this.log('You deflect the monster\'s attack!', 'monster')
                    else this.log('The monster hits you for ' + damage + ' damage.', 'monster');
                }
            }),
            eventBus.$on('upgradePlayer', (obj) => {
                // Update this component with the new details
                if(obj.type == 'weapon') {
                    this.minDmg = obj.item.minDmg;
                    this.maxDmg = obj.item.maxDmg;
                }
                else if(obj.type == 'armor') {
                    this.armorRating = obj.item.rating;
                }
                else {
                    this.healRating = obj.item.rating;
                    this.healCost = obj.item.cost;
                    this.manaRecovery = obj.item.recovery;
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
