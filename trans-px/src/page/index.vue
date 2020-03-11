<template>
    <div class="screen-box">
        <Header></Header>
        <div class="content">
            <div class="control-list">
                <div class="down-box box">
                    <div class="content-box setting">
                        <div class="submit">
                            <div>
                                <TimeSize></TimeSize>
                            </div>
                            <div>
                                <el-button type="primary" style="margin-left:10%" @click="change_time()">设置</el-button>
                            </div>
                        </div>
                        <Barchart></Barchart>
                        <div style="width: 100%;display: flex;justify-content: center;">
                            <el-switch v-model="value1" active-text="流量分析" inactive-text="拥堵分析" @change=change($event)>
                            </el-switch>
                        </div>
                    </div>
                </div>
            </div>
            <div class="show-list">
                <div class="up-box box">
                    <div class="content-box">
                        <Bdmap></Bdmap>
                    </div>
                </div>
                <Slider></Slider>
                <div class="down-box box">
                    <div class="content-box">
                        <Chart></Chart>
                    </div>
                </div>
            </div>
        </div>
        <Footer></Footer>
    </div>
</template>
<script>
    import Bus from '@/util/bus.js'
    //引入标题和脚注
    import Header from '../components/header.vue'
    import Footer from '../components/footer.vue'
    import Bdmap from '../components/bdmap.vue'
    import Chart from '../components/chart.vue'
    import TimeSize from '../components/TimeSize.vue'
    import Barchart from '../components/barchart.vue'
    import Slider from '../components/slider.vue'
    export default {
        components: { Header, Footer, Bdmap, Chart, TimeSize, Barchart, Slider },
        data() {
            return {
                time_select: '',
                value1: true
            }
        },
        methods: {
            change_time() {
                Bus.$emit('change_time_size', this.time_select)
            },
            change: function (status) {
                Bus.$emit('change_type', status)
            }

        }, mounted: function () {
            // 监听此事件
            Bus.$on('select_time_size', (time) => {
                this.time_select = time
            })
        }

    }

</script>
<style scoped>
    @import '../css/index.css';
</style>