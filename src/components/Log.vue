<template>
    <div class="col interface__panel">
        <h2>Game Log</h2>
        <ul>
            <li v-for="log in logs" :key="log.key" :class="log.type">{{ log.msg }}</li>
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
            eventBus.$on('resetTheGame', () => {
                this.reset();
            }),
            eventBus.$on('updateLog', (obj) => {
                // Create an object with a unique ID that is added to the start of the log array
                const uid = (Math.floor(Math.random() * 9000) + 1000) + '' + (Math.floor(Math.random() * 9000) + 1000);
                const logObj = { key: uid, msg: obj.text, type: obj.type};
                // If there are 8 items, remove the last one before adding the latest log
                if(this.logs.length == 8) this.logs.splice(-1, 1);
                this.logs.unshift(logObj);
            })
        }
    }
</script>

<style lang="scss" scoped>
    ul {
        margin: 0;
        padding: 0;
        list-style: none;
    }

    li {
        padding: 5px;
    }

    .player {
        background-color: #84e4ff;
        border-bottom: 1px solid #5b8d9b;
    }

    .monster {
        background-color: #ff8383;
        border-bottom: 1px solid #9b5a5a;
    }
    
    .upgrade {
        background-color: #ffea83;
        border-bottom: 1px solid #998e58;
    }

    .system {
        background-color: #a8ff83;
        border-bottom: 1px solid #6a9957;
        font-weight: 700;
    }
</style>
