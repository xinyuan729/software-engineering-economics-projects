<template>
    <div class="panel">
        <el-card class="list-card">
            <span class="list-title">输入数据预览：</span>
            <el-checkbox class="readOnly" v-model="dataCheck.city">城市</el-checkbox>
            <el-checkbox class="readOnly" v-model="dataCheck.foundData">城市基础数据</el-checkbox>
            <el-checkbox class="readOnly" v-model="dataCheck.simuData">情景模拟数据</el-checkbox>
            <el-checkbox class="readOnly" v-model="dataCheck.hospitalData">医疗服务数据</el-checkbox>
        </el-card>
        <el-collapse @change="onCollapseChange">
            <!-- <el-collapse-item>
                <template slot="title">
                    <span class="collapse-allView">
                        <i class="collapse-icon el-icon-s-help"></i><span class="collapse-title">韧性评价指标总览</span>
                    </span>
                </template>
                <allView />
            </el-collapse-item> -->
            <el-collapse-item>
                <template slot="title">
                    <span class="collapse-currentValue">
                        <i class="collapse-icon el-icon-office-building"></i><span class="collapse-title">城市现状评分</span>
                    </span>
                </template>
                <currentValue ref="currentValue" :selectedCity="taskData.city"
                    :cityfoundDataFiles="taskData.foundationDataFiles" :people="taskData.CityPeople"
                    :area="taskData.CityArea" :dirtything="taskData.AreaDirtyThing" />
            </el-collapse-item>
            <el-collapse-item name="hospitalMeasure">
                <template slot="title">
                    <span class="collapse-hospital">
                        <i class="collapse-icon el-icon-document"></i><span class="collapse-title">医疗服务测度</span>
                    </span>
                </template>
                <div id="aMapHospital" class="aMap" v-loading="mapLoading"></div>
            </el-collapse-item>
        </el-collapse>
    </div>
</template>
<script>
import axios from "axios";
import serverInfo from '@/views/simulator/serverInfo';

import 'intro.js/introjs.css';
import introJs from 'intro.js';

import allView from '@/views/evaluate/allView.vue';
import currentValue from '@/views/evaluate/currentValue.vue';

import { Color } from 'three';

// 新建查看医疗测度地图
const buildAMapHospital = async (bindThis) => {
    const that = bindThis;
    const cityName = that.cityName;
    try {
        that.mapHospital = new AMap.Map('aMapHospital', {
            mapStyle: 'amap://styles/macaron', // 设置地图的显示样式
        });
        setTimeout(function () {
            that.mapHospital.setCity(cityName, function () {
                that.mapmapHospital.setZoom(that.zoomLevel);
            });
        }, 500);


        // 绘制边界
        const districtSearch = new AMap.DistrictSearch({
            level: 'district', // 关键字对应的行政区级别，district表示区/县
            subdistrict: 0, //  显示下级行政区级数，0表示不需要返回下级行政区
            extensions: 'all', // 返回行政区边界坐标组等具体信息
        });
        districtSearch.search(cityName, function (status, result) {
            if (result.info === "OK") {
                var bounds = result.districtList[0].boundaries;
                if (bounds) {
                    // 生成行政区划polygon
                    for (var i = 0; i < bounds.length; ++i) { // 构造MultiPolygon的path
                        bounds[i] = [bounds[i]]
                    }
                    let polygon = new AMap.Polygon({
                        strokeWeight: 2,
                        path: bounds,
                        fillOpacity: 0.0,
                        strokeColor: '#57606f'
                    });
                    that.mapHospital.add(polygon)
                    that.mapHospital.setFitView(polygon);// 视口自适应
                }
            }
        });

    } catch (e) {
        console.error(e, 888);
        that.$message.error(`发生了错误:${JSON.stringify(e)}`);
    }

};


// --- 处理医疗服务数据，转化为点覆盖物 ---
function processHospitalPoints(bindThis, data) {
    const that = bindThis;
    let points = [];

    let valueMax = 0, valueMin = 1;
    data.forEach((item, index) => {
        valueMax = (item.result > valueMax) ? item.result : valueMax;
        valueMin = (item.result < valueMin) ? item.result : valueMin;
    });

    data.forEach((item, index) => {
        let valueNorm = (item.result - valueMin) / (valueMax - valueMin);
        let color = new Color().lerpColors(
            new Color(0x000000),
            new Color(0xff0000),
            valueNorm
        );

        let point = new AMap.Circle({
            center: new AMap.LngLat(item.longtitude, item.latitude),
            radius: 80,
            fillColor: '#' + color.getHexString(),
            fillOpacity: 0.9,
            strokeWeight: 0,
        });

        point.on('click', () => {
            console.log("Click");
            var content = [
                `<center><span style="color:#000">${index}号医疗服务测度点</span></center>`,
                `<span style="color:#000">名称：${item.name}</span>`,
                `<span style="color:#000">地址：${item.address}</span>`,
                `<span style="color:#000">医疗服务测度值：${item.result.toFixed(4)}</span>`,
            ];
            // 创建 infoWindow 实例
            var infoWindow = new AMap.InfoWindow({
                content: content.join("<br>") // 传入 dom 对象，或者 html 字符串
            });
            // 打开信息窗体
            infoWindow.open(that.mapHospital, [item.longtitude, item.latitude]);
        });
        point.on('mouseover', () => {
            point.setOptions({
                fillColor: '#ff8800'
            });
        });
        point.on('mouseout', () => {
            point.setOptions({
                fillColor: '#' + color.getHexString()
            });
        });

        points.push(point);



    });
    let pointsGroup = new AMap.OverlayGroup(points);

    return {
        group: pointsGroup,
        count: pointsGroup.getOverlays().length,
    };

}

export default {
    name: "panelTwo",
    components: {
        allView, currentValue
    },
    data() {
        return {
            dataCheck: {
                city: false,
                foundData: false,
                simuData: false,
                hospitalData: false,
            },

            mapHospital: null,
            zoomLevel: 10,

            cityName: "",
            mapLoading: true,
        };
    },
    props: {
        taskData: {
            type: Object,
        },
        hasHospital: {
            type: Boolean,
        },
    },
    created() { },
    mounted() {
        this.checkData();
        this.introduction();
    },
    methods: {
        assert_len() {
            return this.$refs.currentValue.assert_len();
        },

        saveResults() {
            this.$refs.currentValue.saveResult();
        },

        onCollapseChange(activeNames) {
            if (activeNames.includes("hospitalMeasure")) {
                this.onDisplayHospital();
            }
        },
        checkData() {
            // console.log(this.taskData);
            this.dataCheck.city = "city" in this.taskData;
            this.dataCheck.foundData = "foundationDataFiles" in this.taskData
                && this.taskData.foundationDataFiles.length > 0;
            this.dataCheck.simuData = "type" in this.taskData;
            this.dataCheck.hospitalData = this.hasHospital;
        },
        introduction() {
            introJs().setOptions({
                nextLabel: '下一步',
                prevLabel: '上一步',
                doneLabel: '完成',
                dontShowAgainLabel: '下次不再显示',
                steps: [{
                    intro: '欢迎来到<strong>现状评分</strong>界面！'
                },
                {
                    intro: '查看<strong>城市现状评分</strong>',
                    element: document.querySelector('.collapse-currentValue'),
                },
                {
                    intro: '完成<strong>现状评分</strong>',
                    element: document.querySelector('.dialog3 .dialog-footer button:nth-of-type(2)'),
                },
                ]
            }).start();
        },
        onDisplayHospital() {
            this.mapLoading = true;
            if (!this.hasHospital) {
                this.$message({
                    message: "缺少医疗服务数据！",
                    type: "warning",
                });
                return;
            }
            this.cityName = this.parseCity(this.taskData.city[1]);
            let url = serverInfo.baseURL_measure + "/measure/measureResult?city=" + this.taskData.city[1];
            axios
                .post(url)
                .then((res) => {
                    console.log(res);
                    if (res.data.success) {
                        buildAMapHospital(this);
                        const hospitalResult = processHospitalPoints(this, res.data.data);
                        this.mapHospital.add(hospitalResult.group);
                        setTimeout(() => {
                            this.mapHospital.remove(hospitalResult.group);
                            this.mapHospital.add(hospitalResult.group);
                        }, 2000);
                        this.mapLoading = false;
                    }
                    else {
                        this.$message({
                            message: res.data.errorMsg,
                            type: "warning",
                        });
                        this.mapLoading = true;
                    }
                })
                .catch((err) => {
                    console.log(err);
                    this.$message({
                        message: "获取医疗服务测度失败",
                        type: "error",
                    });
                    this.mapLoading = true;
                });

        },
        parseCity(city) {
            const cityNames = {
                "shanghai": "上海",
                "chongqing": "重庆",
                "guangzhou": "广州",
                "wulumuqi": "乌鲁木齐",
                "ningbo": "宁波",
                "dongying": "东营",
                "weihai": "威海",
                "zibo": "淄博",
                "lianyungang": "连云港",
                "wuxi": "无锡",
                "ezhou": "鄂州",
                "sihui": "四会",
                "jiading": "嘉定",
            };
            if (city in cityNames) {
                return cityNames[city];
            }
            else {
                return city;
            }
        },
    }
};
</script>
<style lang="scss" scoped>
.readOnly {
    pointer-events: none;
}

.list-card {
    margin: 0;

    .list-title {
        font-size: 16px;
        font-weight: bold;
        color: #000;
    }
}

.collapse-title {
    font-size: 16px;
    font-weight: bold;
}

.collapse-icon {
    width: 16px;
    scale: 120%;
}

.aMap {
    width: 100%;
    height: 60vh;
    font-family: "微软雅黑";
    overflow: hidden;
    border-radius: 10px;
    transition: 0.3s;
}
</style>