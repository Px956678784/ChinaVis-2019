<template>
    <div id="barchart"></div>
</template>
<script>
    import Bus from '@/util/bus.js'
    export default {
        data() {
            return {
            }
        },
        mounted() {
            this.drawChart()
        },
        methods: {
            drawChart: function () {
                let dom_rank = document.getElementById("barchart");
                let myChart_bar = this.$echarts.init(dom_rank);
                function roadItemQuantityAnalysis(time_size, time_window, n_road) {
                    $.ajax({
                        url: "http://192.168.1.161:8000/polls/road_item_quantity_analysis/",
                        data: JSON.stringify({
                            time_size: time_size,
                            time_window: time_window,
                            n_road: n_road
                        }),
                        type: 'POST',
                        success: function (res) {
                            let result = res.result;
                            let data = [];
                            let legend_data = ['流入', '流出', '停留'];
                            let road_chose = []
                            result.map(i => {
                                road_chose.push(i[0])
                            })
                            myChart_bar.setOption({
                                tooltip: {
                                    trigger: 'axis',
                                    axisPointer: { // 坐标轴指示器，坐标轴触发有效
                                        type: 'shadow' // 默认为直线，可选为：'line' | 'shadow'
                                    }
                                },
                                grid: {
                                    top: '10%',
                                    bottom: '5%',
                                    left: '30%',
                                    right: '2%'
                                },
                                legend: {
                                    data: legend_data,
                                    textStyle: {
                                        color: '#fff',
                                        fontSize: 10
                                    }
                                },
                                xAxis: {
                                    type: 'value',
                                    axisLine: {
                                        lineStyle: {
                                            color: '#eee'
                                        }
                                    }
                                },
                                yAxis: {
                                    type: 'category',
                                    inverse: true,
                                    axisLine: {
                                        lineStyle: {
                                            color: '#eee'
                                        }
                                    },
                                    data: result.map(i => {
                                        return i[4]
                                    })
                                },
                                series: legend_data.map((val, i) => {
                                    let color = ["#235D79", "#FFCA40", "#FF6840"]
                                    return {
                                        name: val,
                                        type: 'bar',
                                        stack: 'sum',
                                        label: {
                                            normal: {
                                                show: true,
                                                position: 'insideRight'
                                            }
                                        },
                                        itemStyle: {
                                            color: color[i]
                                        },
                                        data: result.map(a => {
                                            return a[i + 1]
                                        })
                                    }
                                })
                            })
                            myChart_bar.on('click', function (params) {
                                Bus.$emit('chosen_road', road_chose[params.dataIndex], params.name)
                            })

                        }

                    })
                }
                let time_size_r = 300, time_window_r = [0, 1]
                roadItemQuantityAnalysis(time_size_r, time_window_r, 20)
                Bus.$on('change_time_size', (new_time_size) => {
                    time_size_r = new_time_size
                    roadItemQuantityAnalysis(time_size_r, time_window_r, 20)
                })
                Bus.$on('change_time_window', (new_time_window) => {
                    time_window_r = new_time_window
                    roadItemQuantityAnalysis(time_size_r, time_window_r, 20)
                })
            }
        }
    }
</script>
<style>
    #barchart {
        width: 100%;
        height: 80%;
    }
</style>