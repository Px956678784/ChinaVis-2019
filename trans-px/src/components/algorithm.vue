<template>
    <el-select v-model="value" placeholder="选择拼车类型">
        <el-option v-for="item in options" :key="item.value" :label="item.label" :value="item.value">
        </el-option>
    </el-select>
</template>

<script>
    export default {
        data() {
            return {
                options: [{
                    value: '选项1',
                    label: '改进的基于区域划分的拼车算法'
                }, {
                    value: '选项2',
                    label: '基于区域划分的拼车算法'
                }, {
                    value: '选项3',
                    label: '最近点优先的双向极限扫描启发式算法'
                }],
                value: ''
            }
        }
    }
</script>