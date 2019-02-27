<template>
    <div class="col interface__panel">
        <h2>Monster
            <span v-if="isMaxLvl">(Level: Maxed)</span>
            <span v-else>(Level: {{ lvl + 1 }})</span>
        </h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: ((health/totalHealth) * 100) + '%' }">{{ health }}</div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    // The monster's stats that increases each time it is defeated
    const stats = [
        { health: 15, minDmg: 1, maxDmg: 1, minGold: 50, maxGold: 75 },
        { health: 30, minDmg: 2, maxDmg: 3, minGold: 50, maxGold: 75 },
        { health: 45, minDmg: 3, maxDmg: 5, minGold: 100, maxGold: 150 },
        { health: 60, minDmg: 4, maxDmg: 7, minGold: 100, maxGold: 150 },
        { health: 75, minDmg: 5, maxDmg: 9, minGold: 150, maxGold: 225 },
        { health: 90, minDmg: 6, maxDmg: 11, minGold: 150, maxGold: 225 },
        { health: 105, minDmg: 7, maxDmg: 13, minGold: 200, maxGold: 300 },
        { health: 120, minDmg: 8, maxDmg: 15, minGold: 200, maxGold: 300 },
        { health: 135, minDmg: 9, maxDmg: 17, minGold: 250, maxGold: 400 },
        { health: 150, minDmg: 10, maxDmg: 19, minGold: 250, maxGold: 400 },
        { health: 165, minDmg: 11, maxDmg: 21, minGold: 300, maxGold: 475 },
        { health: 200, minDmg: 15, maxDmg: 25, minGold: 500, maxGold: 700 }
    ];
    
    export default {
        data() {
            return {
                lvl: 0,
                isMaxLvl: false,
                health: stats[0].health,
                totalHealth: stats[0].health
            }
        },
        methods: {
            reset() {
                this.lvl = 0;
                this.isMaxLvl = false;
                this.health = stats[0].health;
                this.totalHealth = stats[0].health;
            },
            attack() {
                // Roll for damage and emit to the player component
                const roll = Math.floor(Math.random() * (stats[this.lvl].maxDmg - stats[this.lvl].minDmg + 1)) + stats[this.lvl].minDmg;
                eventBus.$emit('monsterAttack', roll);
            },
            lvlUp() {
                // Increase the monster's level until it reaches its maximum
                if(this.lvl < (stats.length - 1)) this.lvl++;
                else this.isMaxLvl = true;

                // Reset the monster's stats based on the level
                this.health = stats[this.lvl].health;
                this.totalHealth = stats[this.lvl].health;
            },
            loot() {
                // Roll for the gold reward and emit to the upgrade component
                const roll = Math.floor(Math.random() * (stats[this.lvl].maxGold - stats[this.lvl].minGold + 1)) + stats[this.lvl].minGold;
                this.log('You defeated the monster! You earned ' + roll + ' gold!');
                eventBus.$emit('rewardLoot', roll);
            },
            log(text) {
                // Send a text message to the log component
                eventBus.$emit('updateLog', text);
            }
        },
        created() {
            eventBus.$on('playerAttack', (damage) => {
                // Take damage from the player's attack
                this.health -= damage;

                // Level up the monster if it died, or it attacks instead
                if(this.health <= 0) { this.lvlUp(); this.loot(); }
                else this.attack();
            });
        }
    }
</script>

<style lang="scss">
</style>
