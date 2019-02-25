<template>
    <div class="col">
        <h2>Upgrades</h2>
        <div class="row">
            <div class="col">
                <h3>Weapon</h3>
                <p>{{ currentWeapon.name }}</p>
                <p>{{ currentWeapon.minDmg }} - {{ currentWeapon.maxDmg }}</p>
                <button @click="upgradeWeapon">Upgrade</button>
            </div>
            <div class="col">
                <h3>Armor Name</h3>
                <p>Armor Rating</p>
                <button>Upgrade</button>
            </div>
            <div class="col">
                <h3>Special Name</h3>
                <p>Special Damage</p>
                <button>Upgrade</button>
            </div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from '../main';

    // List of Weapons
    const weapons = [
        { name: 'Rock', minDmg: 1, maxDmg: 1 },
        { name: 'Stick', minDmg: 1, maxDmg: 2 },
        { name: 'Club', minDmg: 2, maxDmg: 3 },
        { name: 'Stone Spear', minDmg: 2, maxDmg: 4 },
        { name: 'Stone Axe', minDmg: 3, maxDmg: 5 },
        { name: 'Copper Spear', minDmg: 3, maxDmg: 6 },
        { name: 'Copper Axe', minDmg: 4, maxDmg: 7 },
        { name: 'Copper Dagger', minDmg: 4, maxDmg: 8 }
    ];

    export default {
        data() {
            return {
                weaponLvl: 0,
                currentWeapon: weapons[0]
            }
        },
        methods: {
            upgradeWeapon() {
                // If the current weapon level is less than the length of the list of weapons
                if(this.weaponLvl < (weapons.length - 1)) {
                    this.currentWeapon = weapons[++this.weaponLvl]; // Increase the weapon level and set the current weapon
                    eventBus.$emit('updatePlayerDamage', this.currentWeapon); // Emit 'updatePlayerWeapon' to Player component
                }
            }
        }
    }
</script>

<style lang="scss">
</style>
