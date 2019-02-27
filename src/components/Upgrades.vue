<template>
    <div class="col-sm-12 col-md-6 order-2 order-md-1 interface__panel">
        <h2>Upgrades</h2>
        <div class="alert alert-warning" role="alert">
            <p class="mb-0">Gold: {{ gold }}</p>
        </div>
        <div class="row mb-sm-3">
            <div class="col-sm-6">
                <div class="row align-items-center">
                    <div class="col col-sm-12 mb-sm-0">
                        <h3>Weapon</h3>
                        <p class="mb-0">{{ currentWeapon.name }}</p>
                        <p :class="{ 'mb-0': !isWeaponMax }">Damage: {{ currentWeapon.minDmg }} - {{ currentWeapon.maxDmg }}</p>
                        <p v-if="!isWeaponMax">Upgrade: {{ currentWeapon.upgrade }} gold</p>
                    </div>
                    <div class="col col-sm-12 text-center text-sm-left">
                        <button @click="upgrade('weapon')" class="btn btn-warning" :disabled="isWeaponMax || !isPlayerAlive">
                            <span v-if="isWeaponMax">Max</span>
                            <span v-else>Upgrade</span>
                        </button>
                    </div>
                </div>
            </div>
            <div class="col-sm-6">
                <div class="row align-items-center">
                    <div class="col col-sm-12">
                        <h3>Armor</h3>
                        <p class="mb-0">{{ currentArmor.name }}</p>
                        <p :class="{ 'mb-0': !isArmorMax }">Rating: {{ currentArmor.rating }}</p>
                        <p v-if="!isArmorMax">Upgrade: {{ currentArmor.upgrade }} gold</p>
                    </div>
                    <div class="col col-sm-12 text-center text-sm-left">
                        <button @click="upgrade('armor')" class="btn btn-warning" :disabled="isArmorMax || !isPlayerAlive">
                            <span v-if="isArmorMax">Max</span>
                            <span v-else>Upgrade</span>
                        </button>
                    </div>
                </div>
            </div>
        </div>
        <div class="row">
            <div class="col-sm-6">
                <div class="row align-items-center">
                    <div class="col col-sm-12">
                        <h3>Heal</h3>
                        <p :class="{ 'mb-0': !isHealMax }">Rating: {{ currentHeal.rating }}%</p>
                        <p v-if="!isHealMax">Upgrade: {{ currentHeal.upgrade }} gold</p>
                    </div>
                    <div class="col col-sm-12 text-center text-sm-left">
                        <button @click="upgrade('heal')" class="btn btn-warning" :disabled="isHealMax || !isPlayerAlive">
                            <span v-if="isHealMax">Max</span>
                            <span v-else>Upgrade</span>
                        </button>
                    </div>
                </div>
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
        { name: 'Padded', rating: 1, upgrade: 60 },
        { name: 'Leather', rating: 3, upgrade: 90 },
        { name: 'Studded Leather', rating: 5, upgrade: 120 },
        { name: 'Bronze Chainmail', rating: 7, upgrade: 150 },
        { name: 'Bronze Plate', rating: 9, upgrade: 180 },
        { name: 'Iron Chainmail', rating: 11, upgrade: 210 },
        { name: 'Iron Plate', rating: 13, upgrade: 240 },
        { name: 'Steel Chainmail', rating: 15, upgrade: 270 },
        { name: 'Steel Plate', rating: 17, upgrade: 300 },
        { name: 'Mithril Plate', rating: 20, upgrade: 0 }
    ];

    // This spell is pretty important
    const heal = [
        { rating: 1, upgrade: 40 },
        { rating: 2, upgrade: 80 },
        { rating: 4, upgrade: 120 },
        { rating: 6, upgrade: 160 },
        { rating: 8, upgrade: 200 },
        { rating: 10, upgrade: 240 },
        { rating: 12, upgrade: 280 },
        { rating: 14, upgrade: 320 },
        { rating: 16, upgrade: 360 },
        { rating: 18, upgrade: 400 },
        { rating: 20, upgrade: 0 }
    ];

    export default {
        props: ['isPlayerAlive'],
        data() {
            return {
                isWeaponMax: false,
                isArmorMax: false,
                isHealMax: false,
                gold: 0,
                weaponLvl: 0,
                armorLvl: 0,
                healLvl: 0,
                currentWeapon: weapons[0],
                currentArmor: armor[0],
                currentHeal: heal[0]
            }
        },
        methods: {
            reset() {
                this.isWeaponMax = false;
                this.isArmorMax = false;
                this.isHealMax = false;
                this.gold = 0;
                this.weaponLvl = 0;
                this.armorLvl = 0;
                this.healLvl = 0;
                this.currentWeapon = weapons[0];
                this.currentArmor = armor[0];
                this.currentHeal = heal[0];
            },
            log(text, type = 'upgrade') {
                // Send a text message to the log component
                const logObj = { text: text, type: type }
                eventBus.$emit('updateLog', logObj);
            },
            upgrade(type) {
                let item, itemLvl, itemMaxLvl;

                // Set the item depending on the type
                if(type == 'weapon') {
                    item = this.currentWeapon;
                    itemLvl = this.weaponLvl;
                    itemMaxLvl = weapons.length - 1;
                }
                else if(type == 'armor') {
                    item = this.currentArmor;
                    itemLvl = this.armorLvl;
                    itemMaxLvl = armor.length - 1;
                }
                else {
                    item = this.currentHeal;
                    itemLvl = this.healLvl;
                    itemMaxLvl = heal.length - 1;
                }

                // Run this only if the player has enough gold
                if(this.gold >= item.upgrade) {
                    this.gold -= item.upgrade;

                    if(itemLvl++ < itemMaxLvl) {
                        if(type == 'weapon') this.weaponLvl++;
                        else if(type == 'armor') this.armorLvl++;
                        else this.healLvl++;
                    }

                    if(itemLvl == itemMaxLvl) {
                        if(type == 'weapon') this.isWeaponMax = true;
                        else if(type == 'armor') this.isArmorMax = true;
                        else this.isHealMax = true;
                    }

                    if(type == 'weapon') item = (this.currentWeapon = weapons[itemLvl]);
                    else if(type == 'armor') item = (this.currentArmor = armor[itemLvl]);
                    else item = (this.currentHeal = heal[itemLvl])

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
