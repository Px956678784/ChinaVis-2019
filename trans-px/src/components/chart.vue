<template>
    <div id="chart"></div>
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
                let dom_timechart = document.getElementById("chart");
                let time_chart = this.$echarts.init(dom_timechart);
                let id = null, type = true, road_name = ""
                function drawlines(road_id) {
                    let target, dataid
                    if (road_id == null && type == true) {
                        target = "http://192.168.1.161:8000/polls/time_chart/"
                        dataid = null
                    }
                    else if (type == true) {
                        target = "http://192.168.1.161:8000/polls/road_quantity_time_chart/"
                        dataid = JSON.stringify({
                            fid: road_id
                        })
                    }
                    else {
                        target = "http://192.168.1.161:8000/polls/road_speed_time_chart/"
                        dataid = JSON.stringify({
                            fid: road_id
                        })
                    }
                    $.ajax({
                        url: target,
                        data: dataid,
                        type: 'POST',
                        success: function (res) {

                            let result = res.result;
                            time_chart.setOption({
                                title: {
                                    text: road_name,
                                    textStyle: {
                                        color: '#eee',
                                        fontSize: 15
                                    }
                                },
                                grid: {
                                    top: '18%',
                                    bottom: '15%',
                                    left: '5%',
                                    right: '2%'
                                },
                                brush: {
                                    toolbox: ['lineX', 'lineY', 'keep', 'clear']
                                },
                                xAxis: {
                                    type: 'category',
                                    data: new Array(result.length).fill().map((val, i) => {
                                        function PrefixInteger(num, n) {
                                            return (Array(n).join(0) + num).slice(-n);
                                        }
                                        return PrefixInteger(Math.floor(i * 5 / 60), 2) + ":" + PrefixInteger(i * 5 % 60, 2);

                                    }),
                                    name: 'time(per 5 min)',
                                    nameLocation: 'middle',
                                    nameGap: 30,
                                    nameTextStyle: {
                                        fontSize: 18,
                                        color: 'white'
                                    },
                                    axisLine: {
                                        lineStyle: {
                                            color: '#eee'
                                        }
                                    }

                                },
                                yAxis: {
                                    type: 'value',
                                    axisLine: {
                                        lineStyle: {
                                            color: '#eee'
                                        }
                                    }
                                },
                                series: [{
                                    data: result.map((val, i) => {
                                        return val[1];
                                    }),
                                    type: 'line',
                                    lineStyle: {
                                        color: '#FF6840',
                                        width: 3,
                                        opacity: 0.7
                                    },
                                }]
                            })
                        }
                    })
                }
                drawlines(id)
                Bus.$on('chosen_road', (new_id, new_name) => {
                    type = true
                    road_name = new_name
                    id = new_id
                    drawlines(id)
                })
                Bus.$on('chosen_road_speed', (new_id, new_name) => {
                    type = false
                    road_name = new_name
                    id = new_id
                    drawlines(id)
                })
            }
        }
    }
</script>
<style>
    #chart {
        flex: 1;
    }
</style>