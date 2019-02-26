<template>
    <div class="col">
        <h2>Upgrades</h2>
        <div class="row">
            <div class="col">
                <p>Currency: ${{ gold }}</p>
            </div>
        </div>
        <div class="row">
            <div class="col">
                <h3>Weapon</h3>
                <p>{{ currentWeapon.name }}</p>
                <p>{{ currentWeapon.minDmg }} - {{ currentWeapon.maxDmg }}</p>
                <button v-if="isMaxWeapon" disabled>Max Weapon</button>
                <button v-else @click="upgradeWeapon">Upgrade (${{ currentWeapon.upgradeCost}})</button>
            </div>
            <div class="col">
                <h3>Armor</h3>
                <p>{{ currentArmor.name }}</p>
                <p>{{ currentArmor.rating }}</p>
                <button v-if="isMaxArmor" disabled>Max Armor</button>
                <button v-else @click="upgradeArmor">Upgrade (${{ currentArmor.upgradeCost}})</button>
            </div>
            <div class="col">
                <h3>Special Name</h3>
                <p>Special Damage</p>
                <button disabled>Upgrade</button>
            </div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from '../main';

    // List of Weapons
    const weapons = [
        { name: 'Rock', minDmg: 1, maxDmg: 1, upgradeCost: 10 },
        { name: 'Stick', minDmg: 1, maxDmg: 2, upgradeCost: 15 },
        { name: 'Club', minDmg: 2, maxDmg: 3, upgradeCost: 20 },
        { name: 'Stone Spear', minDmg: 2, maxDmg: 4, upgradeCost: 25 },
        { name: 'Stone Axe', minDmg: 3, maxDmg: 5, upgradeCost: 30 },
        { name: 'Copper Spear', minDmg: 3, maxDmg: 6, upgradeCost: 40 },
        { name: 'Copper Axe', minDmg: 4, maxDmg: 7, upgradeCost: 50 },
        { name: 'Copper Dagger', minDmg: 4, maxDmg: 8, upgradeCost: 0 }
    ];

    // List of Armor
    const armor = [
        { name: 'None', rating: 0, upgradeCost: 10 },
        { name: 'Padded Cloth', rating: 1, upgradeCost: 20 },
        { name: 'Animal Skins', rating: 2, upgradeCost: 30 },
        { name: 'Leather', rating: 3, upgradeCost: 40 },
        { name: 'Copper', rating: 4, upgradeCost: 50 },
        { name: 'Bronze', rating: 5, upgradeCost: 0 },
    ];

    export default {
        data() {
            return {
                isMaxWeapon: false,
                isMaxArmor: false,
                gold: 1000,
                weaponLvl: 0,
                armorLvl: 0,
                currentWeapon: weapons[0],
                currentArmor: armor[0]
            }
        },
        methods: {
            upgradeWeapon() {
                // If the current weapon level is less than the length of the list of weapons
                if(this.weaponLvl < (weapons.length - 1)) {
                    // Allow upgrade only if player has enough money
                    if(this.gold >= this.currentWeapon.upgradeCost) {
                        this.gold -= this.currentWeapon.upgradeCost; // Deduct upgrade cost from money
                        this.currentWeapon = weapons[++this.weaponLvl]; // Increase the weapon level and set the current weapon
                        eventBus.$emit('updatePlayerDamage', this.currentWeapon); // Emit 'updatePlayerWeapon' to Player component

                        // Emit 'printLog' trigger to Log component
                        eventBus.$emit('printLog', {
                            timestamp: 'U-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                            text: 'You upgraded your weapon.'
                        });

                        // If this is the last weapon, set 'isMaxWeapon' to true
                        if(this.weaponLvl == (weapons.length - 1)) {
                            this.isMaxWeapon = true;
                        }
                    }
                    else {
                        this.moneyNotEnough();
                    }
                }
                else {
                    // Emit 'printLog' trigger to Log component
                    eventBus.$emit('printLog', {
                        timestamp: 'U-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                        text: 'You\'ve maxed out your weapon upgrade.'
                    });
                }
            },
            upgradeArmor() {
                // If the current armor level is less than the length of the list of armor
                if(this.armorLvl < (armor.length - 1)) {
                    // Allow upgrade only if player has enough money
                    if(this.gold >= this.currentArmor.upgradeCost) {
                        this.gold -= this.currentArmor.upgradeCost; // Deduct upgrade cost from money
                        this.currentArmor = armor[++this.armorLvl]; // Increase the weapon level and set the current weapon
                        eventBus.$emit('updatePlayerArmor', this.currentArmor); // Emit 'updatePlayerArmor' to Player component

                        // Emit 'printLog' trigger to Log component
                        eventBus.$emit('printLog', {
                            timestamp: 'U-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                            text: 'You upgraded your armor.'
                        });

                        // If this is the last armor, set 'isMaxArmor' to true
                        if(this.armorLvl == (armor.length - 1)) {
                            this.isMaxArmor = true;
                        }
                    }
                    else {
                        this.moneyNotEnough();
                    }
                }
                else {
                    // Emit 'printLog' trigger to Log component
                    eventBus.$emit('printLog', {
                        timestamp: 'U-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                        text: 'You\'ve maxed out your armor upgrade.'
                    });
                }
            },
            moneyNotEnough() {
                // Emit 'printLog' trigger to Log component
                eventBus.$emit('printLog', {
                    timestamp: 'U-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                    text: 'You have insufficient funds!'
                });
            }
        },
        created() {
            // Listen for 'resetEverything' emit trigger from App
            eventBus.$on('resetEverything', () => {
                this.isMaxWeapon = false;
                this.isMaxArmor = false;
                this.gold = 100;
                this.weaponLvl = 0;
                this.armorLvl = 0;
                this.currentWeapon = weapons[0];
                this.currentArmor = armor[0];
            }),
            // Listen for 'getReward' emit trigger from App
            eventBus.$on('getReward', () => {
                const roll = Math.max((Math.floor(Math.random() * 50) + 1), 25);
                this.gold += roll;

                // Emit 'printLog' trigger to Log component
                eventBus.$emit('printLog', {
                    timestamp: 'U-' + Date.now(), // Timestamp used to generate key for the v-bind:key
                    text: 'You earned $' + roll + '!'
                });
            })
        }
    }
</script>

<style lang="scss">
</style>
