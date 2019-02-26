<template>
    <div class="col interface__panel">
        <h2>Upgrades</h2>
        <div class="row">
            <div class="col">
                Total Gold: {{ gold }}
            </div>
        </div>
        <div class="row">
            <div class="col">
                <h3>Weapon</h3>
            </div>
            <div class="col">
                <h3>Armor</h3>
            </div>
        </div>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    export default {
        data() {
            return {
                gold: 0
            }
        },
        methods: {
            reset() {
                this.gold = 0;
            }
        },
        created() {
            eventBus.$on('rewardLoot', (gold) =>{
                this.gold += gold;
            })
        }
    }
</script>

<style lang="scss">
</style>
