<template>
    <div class="col interface__panel">
        <h2>Upgrades</h2>
        <p>Total Gold: {{ gold }}</p>
        <div class="row">
            <div class="col">
                <h3>Weapon</h3>
                <p class="mb-0">{{ currentWeapon.name }}</p>
                <p>Damage: {{ currentWeapon.minDmg }} - {{ currentWeapon.maxDmg }}</p>
                <button @click="upgrade('weapon')" :disabled="isWeaponMax || !isPlayerAlive">
                    <span v-if="isWeaponMax">Max Reached</span>
                    <span v-else>Upgrade ({{ currentWeapon.upgrade }} gold)</span>
                </button>
            </div>
            <div class="col">
                <h3>Armor</h3>
                <p class="mb-0">{{ currentArmor.name }}</p>
                <p>Rating: {{ currentArmor.rating }}</p>
                <button @click="upgrade('armor')" :disabled="isWeaponMax || !isPlayerAlive">
                    <span v-if="isArmorMax">Max Reached</span>
                    <span v-else>Upgrade ({{ currentArmor.upgrade }} gold)</span>
                </button>
            </div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    // Look at all these lovely weapons
    const weapons = [
        { name: 'Rock', minDmg: 1, maxDmg: 1, upgrade: 20 },
        { name: 'Club', minDmg: 1, maxDmg: 2, upgrade: 40 },
        { name: 'Wooden Sword', minDmg: 2, maxDmg: 3, upgrade: 60 },
        { name: 'Blunt Sword', minDmg: 2, maxDmg: 4, upgrade: 80 },
        { name: 'Bronze Axe', minDmg: 3, maxDmg: 5, upgrade: 100 },
        { name: 'Bronze Sword', minDmg: 3, maxDmg: 6, upgrade: 120 },
        { name: 'Iron Axe', minDmg: 4, maxDmg: 7, upgrade: 140 },
        { name: 'Iron Sword', minDmg: 4, maxDmg: 8, upgrade: 160 },
        { name: 'Steel Axe', minDmg: 5, maxDmg: 9, upgrade: 180 },
        { name: 'Steel Sword', minDmg: 5, maxDmg: 10, upgrade: 200 },
        { name: 'Mithril Sword', minDmg: 10, maxDmg: 15, upgrade: 0 }
    ];

    // You're gonna need this
    const armor = [
        { name: 'None', rating: 0, upgrade: 30 },
        { name: 'Padded', rating: 2, upgrade: 60 },
        { name: 'Leather', rating: 4, upgrade: 90 },
        { name: 'Studded Leather', rating: 6, upgrade: 120 },
        { name: 'Bronze Chainmail', rating: 8, upgrade: 150 },
        { name: 'Bronze Plate', rating: 10, upgrade: 180 },
        { name: 'Iron Chainmail', rating: 12, upgrade: 210 },
        { name: 'Iron Plate', rating: 14, upgrade: 240 },
        { name: 'Steel Chainmail', rating: 16, upgrade: 270 },
        { name: 'Steel Plate', rating: 18, upgrade: 300 },
        { name: 'Mithril Plate', rating: 20, upgrade: 0 }
    ];

    export default {
        props: ['isPlayerAlive'],
        data() {
            return {
                isWeaponMax: false,
                isArmorMax: false,
                gold: 0,
                weaponLvl: 0,
                armorLvl: 0,
                currentWeapon: weapons[0],
                currentArmor: armor[0]
            }
        },
        methods: {
            reset() {
                this.isWeaponMax = false;
                this.isArmorMax = false;
                this.gold = 0;
                this.weaponLvl = 0;
                this.armorLvl = 0;
                this.currentWeapon = weapons[0];
                this.currentArmor = armor[0];
            },
            log(text, type = 'upgrade') {
                // Send a text message to the log component
                const logObj = { text: text, type: type }
                eventBus.$emit('updateLog', logObj);
            },
            upgrade(type) {
                // Set the item depending if it is a weapon or armor
                let item = this.currentWeapon,
                    itemLvl = this.weaponLvl,
                    itemMaxLvl = weapons.length - 1;

                if(type == 'armor') {
                    item = this.currentArmor;
                    itemLvl = this.armorLvl;
                    itemMaxLvl = armor.length - 1;
                }

                // Run this only if the player has enough gold
                if(this.gold >= item.upgrade) {
                    this.gold -= item.upgrade;
                    if(itemLvl++ < itemMaxLvl) { type == 'weapon' ? this.weaponLvl++ : this.armorLvl++; }
                    if(itemLvl == itemMaxLvl) { type == 'weapon' ? this.isWeaponMax = true : this.isArmorMax = true; }
                    type == 'weapon' ? item = (this.currentWeapon = weapons[itemLvl]) : item = (this.currentArmor = armor[itemLvl]);

                    // Send the upgrade details to the player component
                    const itemObj = { type: type, item: item };
                    eventBus.$emit('upgradePlayer', itemObj);
                }
                else this.log('You have insufficient gold!');
            }
        },
        created() {
            eventBus.$on('resetTheGame', () => {
                this.reset();
            }),
            eventBus.$on('rewardLoot', (gold) =>{
                // Reward the player with an amount of gold
                this.gold += gold;
            })
        }
    }
</script>

<style lang="scss">
</style>
