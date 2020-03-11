<template>
    <div id="outbox">
        <div id="allmap"></div>
        <div id="samplebox"></div>
    </div>
</template>

<script>
    import Bus from '@/util/bus.js'
    import $ from 'jquery'
    export default {
        data() {
            return {}
        },
        mounted() {
            this.ready()
        },
        methods: {
            ready: function () {
                var colors = ['#BEEF99', '#99D555', '#55D6BE', '#23F0C7', '#FFCF56', '#FCA17D', '#D85494', '#9A348E', '#6F4FFF', '#6F4FFF']
                var colorNum = 0
                var Booking_Data = []
                let linemap = document.getElementById("outbox");
                let myMap = this.$echarts.init(linemap);
                let mapStyle = {
                    'styleJson': [
                        {
                            'featureType': 'water',
                            'elementType': 'all',
                            'stylers': {
                                'color': '#031628'
                            }
                        },
                        {
                            'featureType': 'land',
                            'elementType': 'geometry',
                            'stylers': {
                                'color': '#000102'
                            }
                        },
                        {
                            'featureType': 'highway',
                            'elementType': 'all',
                            'stylers': {
                                'visibility': 'off'
                            }
                        },
                        {
                            'featureType': 'arterial',
                            'elementType': 'geometry.fill',
                            'stylers': {
                                'color': '#000000'
                            }
                        },
                        {
                            'featureType': 'arterial',
                            'elementType': 'geometry.stroke',
                            'stylers': {
                                'color': '#0b3d51'
                            }
                        },
                        {
                            'featureType': 'local',
                            'elementType': 'geometry',
                            'stylers': {
                                'color': '#000000'
                            }
                        },
                        {
                            'featureType': 'railway',
                            'elementType': 'geometry.fill',
                            'stylers': {
                                'color': '#000000'
                            }
                        },
                        {
                            'featureType': 'railway',
                            'elementType': 'geometry.stroke',
                            'stylers': {
                                'color': '#08304b'
                            }
                        },
                        {
                            'featureType': 'subway',
                            'elementType': 'geometry',
                            'stylers': {
                                'lightness': -70
                            }
                        },
                        {
                            'featureType': 'building',
                            'elementType': 'geometry.fill',
                            'stylers': {
                                'color': '#000000'
                            }
                        },
                        {
                            'featureType': 'all',
                            'elementType': 'labels.text.fill',
                            'stylers': {
                                'color': '#857f7f'
                            }
                        },
                        {
                            'featureType': 'all',
                            'elementType': 'labels.text.stroke',
                            'stylers': {
                                'color': '#000000'
                            }
                        },
                        {
                            'featureType': 'building',
                            'elementType': 'geometry',
                            'stylers': {
                                'color': '#022338'
                            }
                        },
                        {
                            'featureType': 'green',
                            'elementType': 'geometry',
                            'stylers': {
                                'color': '#062032'
                            }
                        },
                        {
                            'featureType': 'boundary',
                            'elementType': 'all',
                            'stylers': {
                                'color': '#465b6c'
                            }
                        },
                        {
                            'featureType': 'manmade',
                            'elementType': 'all',
                            'stylers': {
                                'color': '#022338'
                            }
                        },
                        {
                            'featureType': 'label',
                            'elementType': 'all',
                            'stylers': {
                                'visibility': 'off'
                            }
                        }
                    ]
                }
                function drawLines(type, time_size, time_window) {
                    if (type == true) {
                        $.ajax({
                            url: "http://192.168.1.161:8000/polls/road_segment_quantity/",
                            data: JSON.stringify({
                                time_size: time_size,
                                time_window: time_window
                            }),
                            type: 'POST',
                            success: function (res) {
                                let chosen_id
                                let result = res.result
                                var Lines = []
                                for (let i in result) {
                                    let line = new Object()
                                    line.coords = eval(result[i].ploy_line).map(i => {
                                        return [i[0] + 0.0065, i[1] + 0.006]
                                    }),
                                        line.lineStyle = {
                                            normal: {
                                                color: '#5A94DF'
                                            },

                                        }
                                    line.fid = result[i].fid
                                    line.value = [result[i].num]
                                    line.name = result[i].name
                                    Lines.push(line)

                                }
                                let option = {
                                    bmap: {
                                        center: [104.041498, 30.466632],
                                        zoom: 13,
                                        roam: true,
                                        mapStyle: mapStyle
                                    }, visualMap: {
                                        type: 'continuous',
                                        min: 0,
                                        max: 500,
                                        text: ['High', 'Low'],
                                        textStyle: {
                                            fontSize: 10,
                                            color: 'white'
                                        },
                                        dimention: 0,
                                        realtime: false,
                                        calculable: true,
                                        color: ['orangered', 'yellow', 'lightskyblue']
                                    },
                                    series: [{
                                        type: 'lines',
                                        coordinateSystem: 'bmap',
                                        polyline: true,
                                        data: Lines,
                                        //        silent: true,
                                        lineStyle: {
                                            normal: {
                                                opacity: 0.6,
                                                width: 2
                                            },
                                            emphasis: {
                                                width: 5,
                                                opacity: 1
                                            }
                                        },
                                        progressiveThreshold: 500,
                                        progressive: 200
                                    }, {
                                        type: 'lines',
                                        coordinateSystem: 'bmap',
                                        polyline: true,
                                        data: Lines,
                                        lineStyle: {
                                            normal: {
                                                width: 0
                                            }
                                        },
                                        effect: {
                                            constantSpeed: 20,
                                            show: true,
                                            trailLength: 0.1,
                                            symbolSize: 1.5
                                        },
                                        zlevel: 1
                                    }]
                                }

                                if (option && typeof option === "object") {
                                    myMap.clear()
                                    myMap.setOption(option, true)
                                    myMap.off('click')
                                    /*Bus.$on('chosen_road', (id, name) => {
                                        myMap.dispatchAction({
                                            type: 'downplay',
                                            // 可选，系列 index，可以是一个数组指定多个系列
                                            seriesIndex: 0,
                                            // 可选，系列名称，可以是一个数组指定多个系列
                                            fid: chosen_id
                                        })
                                        chosen_id = id
                                        myMap.dispatchAction({
                                            type: 'highlight',
                                            // 可选，系列 index，可以是一个数组指定多个系列
                                            seriesIndex: 0,
                                            // 可选，数据的 名称
                                            fid: id
                                        })
                                    })*/
                                    myMap.on('click', function (params) {
                                        if (params.componentSubType == "lines") {
                                            Bus.$emit('chosen_road', params.data.fid, params.data.name)
                                        }
                                    })

                                }

                            }

                        })
                    }
                    else {
                        $.ajax({
                            url: "http://192.168.1.161:8000/polls/road_segment_speed/",
                            data: JSON.stringify({
                                time_size: time_size,
                                time_window: time_window
                            }),
                            type: 'POST',
                            success: function (res) {
                                console.log(res);
                                let result = res.result;
                                let data = [];
                                result.map((val, i) => {
                                    // console.log(val.ploy_line);
                                    data.push({
                                        coords: eval(val.ploy_line).map(i => {
                                            return [i[0] + 0.0065, i[1] + 0.006]
                                        }),
                                        // lineStyle: {
                                        // normal: {
                                        // width: 3
                                        // }
                                        // },
                                        value: [val.num],
                                        // num: val.num,
                                        fid: val.fid,
                                        name: val.name
                                    })

                                })
                                myMap.clear()
                                myMap.setOption(
                                    {
                                        visualMap: {
                                            bottom: 20,
                                            right: 20,
                                            pieces: [{
                                                gt: 40,

                                                color: '#00b894'
                                            }, {
                                                gt: 30,
                                                lte: 40,
                                                color: '#55efc4'
                                            }, {
                                                gt: 20,
                                                lte: 30,
                                                color: '#fdcb6e'
                                            }, {
                                                gt: 15,
                                                lte: 20,
                                                color: '#e17055'
                                            }, {
                                                gt: 0,
                                                lte: 15,
                                                color: '#d63031'
                                            }],
                                            borderColor: '#ccc',
                                            borderWidth: 2,
                                            backgroundColor: '#eee',
                                            dimension: 0,
                                            inRange: {
                                                // color: COLORS,
                                                opacity: 0.7
                                            }
                                        },
                                        bmap: {
                                            center: [104.040847, 30.466655],
                                            zoom: 13,
                                            roam: true,
                                            mapStyle: mapStyle
                                        },
                                        series: [{
                                            type: 'lines',
                                            coordinateSystem: 'bmap',
                                            polyline: true,
                                            data: data,
                                            lineStyle: {
                                                width: 3
                                            },
                                            emphasis: {
                                                lineStyle: {
                                                    width: 5,
                                                    opacity: 1
                                                }
                                            }
                                        }]
                                    }
                                );
                                myMap.off('click')
                                myMap.on('click', function (params) {
                                    if (params.componentSubType == "lines") {
                                        console.log(params.data)
                                        Bus.$emit('chosen_road_speed', params.data.fid, params.data.name)
                                    }
                                })
                            }

                        })
                    }

                }
                let time_size_n = 300, time_window_n = [0, 1], type = true
                drawLines(type, time_size_n, time_window_n)
                //监听轨迹重绘请求
                Bus.$on('change_type', (new_type) => {
                    type = new_type
                    drawLines(type, time_size_n, time_window_n)
                })
                Bus.$on('change_time_size', (new_time_size) => {
                    time_size_n = new_time_size
                    drawLines(type, time_size_n, time_window_n)
                })
                Bus.$on('change_time_window', (new_time_window) => {
                    time_window_n = new_time_window
                    drawLines(type, time_size_n, time_window_n)
                })
            }
        }
    }
</script>
<style>
    #outbox {
        position: relative;
        flex: 1;
        display: flex;
    }

    #allmap {
        flex: 1;
    }

    #samplebox {
        width: 100%;
        height: 7%;
        position: absolute;
        top: 0;
        display: flex;
    }

    .anchorBL {

        display: none;

    }
</style>