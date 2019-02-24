<template>
    <div class="col">
        <h2>Monster</h2>
        <div class="progress">
            <div class="progress-bar bg-success" :style="{ width: health + '%' }">{{ health }}</div>
        </div>
        {{ test }}
    </div>
</template>

<script>
    import { eventBus } from '../main';

    export default {
        data() {
            return {
                health: 100,
                attack: 2
            }
        },
        created() {
            eventBus.$on('playerHasAttacked', (damage) => {
                this.health -= damage;
                eventBus.$emit('monsterHasAttacked', this.attack);
            });
        }
    }
</script>

<style lang="scss">
</style>
