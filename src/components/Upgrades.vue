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
                <h3>Armor</h3>
                <p>{{ currentArmor.name }}</p>
                <p>{{ currentArmor.rating }}</p>
                <button @click="upgradeArmor">Upgrade</button>
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

    // List of Armor
    const armor = [
        { name: 'None', rating: 0 },
        { name: 'Padded Cloth', rating: 1 },
        { name: 'Animal Skins', rating: 2 },
        { name: 'Leather', rating: 3 },
        { name: 'Copper', rating: 4 },
        { name: 'Bronze', rating: 5 },
    ];

    export default {
        data() {
            return {
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
                    this.currentWeapon = weapons[++this.weaponLvl]; // Increase the weapon level and set the current weapon
                    eventBus.$emit('updatePlayerDamage', this.currentWeapon); // Emit 'updatePlayerWeapon' to Player component
                }
            },
            upgradeArmor() {
                // If the current armor level is less than the length of the list of armor
                if(this.armorLvl < (armor.length - 1)) {
                    this.currentArmor = armor[++this.armorLvl]; // Increase the weapon level and set the current weapon
                    eventBus.$emit('updatePlayerArmor', this.currentArmor); // Emit 'updatePlayerArmor' to Player component
                }
            }
        }
    }
</script>

<style lang="scss">
</style>
