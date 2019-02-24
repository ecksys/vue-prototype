<template>
    <div class="col">
        <h2>Combat Log</h2>
        <ul>
            <li v-for="log in logs">{{ log.text }}</li>
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
            // Listen for 'printLog' emit trigger
            eventBus.$on('printLog', (log) => {
                // If the log has more than 9 entries, remove the last one
                if(this.logs.length > 9) {
                    this.logs.splice(-1,1);
                }

                // Add the latest log to the start of the array
                this.logs.unshift(log);
            })
        }
    }
</script>

<style lang="scss">
</style>
