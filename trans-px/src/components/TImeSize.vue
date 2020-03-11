<template>
    <el-select v-model="value" placeholder="时间片大小" @change="currentSel">
        <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
    </el-select>
</template>

<script>
    import Bus from '@/util/bus.js'
    export default {
        data() {
            return {
                options: [{
                    value: '选项1',
                    label: '5分钟'
                }, {
                    value: '选项2',
                    label: '10分钟'
                },
                {
                    value: '选项3',
                    label: '30分钟'
                },
                {
                    value: '选项4',
                    label: '1小时'
                },
                {
                    value: '选项5',
                    label: '2小时'
                }],
                value: ''

            }
        },
        methods: {
            currentSel(selVal) {
                let obj = {}
                obj = this.options.find((item) => {
                    return item.value === selVal;//筛选出匹配数据
                });
                let time = obj.label.slice(0, -2)
                if (time < 5) time *= 3600
                else time *= 60
                Bus.$emit('select_time_size', time)
            }
        }
    }
</script>