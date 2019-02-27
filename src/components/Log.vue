<template>
    <div class="col interface__panel">
        <h2>Game Log</h2>
        <ul>
            <li v-for="log in logs" :key="log.key">{{ log.msg }}</li>
        </ul>
    </div>
</template>

<script>
    import { eventBus } from '../main';
    
    export default {
        data() {
            return {
                logs: []
            }
        },
        methods: {
            reset() {
                this.logs = [];
            }
        },
        created() {
            eventBus.$on('updateLog', (text) => {
                // Create an object with a unique ID that is added to the start of the log array
                const uid = (Math.floor(Math.random() * 9000) + 1000) + '' + (Math.floor(Math.random() * 9000) + 1000);
                const obj = { key: uid, msg: text};
                if(this.logs.length == 8) this.logs.splice(-1, 1);
                this.logs.unshift(obj);
            })
        }
    }
</script>

<style lang="scss">
</style>
