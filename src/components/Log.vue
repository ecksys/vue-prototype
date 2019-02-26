<template>
    <div class="col">
        <h2>Combat Log</h2>
        <ul>
            <li v-for="log in logs" :key="log.timestamp">{{ log.text }}</li>
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
        created() {
            // Listen for 'printLog' emit trigger from Player/Monster components
            eventBus.$on('printLog', (log) => {
                // If the log has more than 9 entries, remove the last one
                if(this.logs.length > 9) {
                    this.logs.splice(-1,1);
                }

                this.logs.unshift(log); // Add the latest log to the start of the array
            }),
            // Listen for 'resetEverything' emit trigger from App
            eventBus.$on('resetEverything', () => {
                this.logs = [];
            })
        }
    }
</script>

<style lang="scss">
</style>
