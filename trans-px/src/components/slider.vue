<template>
    <div class="block">
        <el-slider v-model="value1" @change="changewindow" :format-tooltip="formatTooltip" :step="time_size_s"
            :max="1440">
        </el-slider>
    </div>
</template>
<script>
    import Bus from '@/util/bus.js'
    export default {
        data() {
            return {
                value1: 0,
                time_size_s: 5
            }
        }, mounted() {
            this.ready()
        },
        methods: {
            ready: function () {
                Bus.$on('change_time_size', (new_time_size) => {
                    new_time_size /= 60
                    this.time_size_s = new_time_size
                })
            },
            formatTooltip(val) {
                function PrefixInteger(num, n) {
                    return (Array(n).join(0) + num).slice(-n);
                }
                return PrefixInteger(Math.floor(val / 60), 2) + ":" + PrefixInteger(val % 60, 2);
            },
            changewindow(value) {
                Bus.$emit('change_time_window', [value / 5, value / 5 + 1])
            }
        }
    }
</script>
<style>
    .block {
        padding-left: 3%;
        padding-right: 3%;
    }
</style>