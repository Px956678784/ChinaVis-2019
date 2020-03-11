<template>
    <div class="block">
        <el-time-select v-model="value1" :picker-options="{
                  start: '07:00',
                  step: '01:00',
                  end: '23:00'
                }" placeholder="选择订单时间" size="mini">
        </el-time-select>
    </div>
</template>
<script>
    export default {
        data() {
            return {
                value1: ''
            };
        }
    }
</script>