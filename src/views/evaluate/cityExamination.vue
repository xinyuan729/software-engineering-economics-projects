<template>
    <div class="app-container">
        <el-row :gutter="10">
            <el-col :span="10">
                <el-card>
                    <div slot="header">
                        <span>韧性城市体检评估</span>
                        <div style="position: relative; float: right; bottom: 10px;">
                            <el-cascader :options="options" placeholder="城市" :show-all-levels="false"
                                v-model="cityValue" @change="recordCity" style="width: 200px">
                                <template slot-scope="{ node, data }">
                                    <span>{{ data.label }}</span>
                                    <span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
                                </template>
                            </el-cascader>
                        </div>
                    </div>
                    <div>
                        <span>城市韧性敏感词</span>
                        <el-divider></el-divider>
                        <div id="roughnessWordCloud" ref="loginTimes" style="width: 540px; height: 360px;"></div>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="14">
                <el-card>
                    <div slot="header">
                        <span>城市区块韧性展示</span>
                    </div>
                    <div id="resilienceBlock" style="width: 100%; height: 430px;"></div>
                </el-card>
            </el-col>
        </el-row>
        <el-row :gutter="10" style="position:relative; top:5px">
            <el-col :span="6">
                <el-card>
                    <div slot="header">
                        <span>报告1</span>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card>
                    <div slot="header">
                        <span>报告2</span>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card>
                    <div slot="header">
                        <span>报告3</span>
                    </div>
                </el-card>
            </el-col>
            <el-col :span="6">
                <el-card>
                    <div slot="header">
                        <span>报告4</span>
                    </div>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>
  
<script>
import * as echarts from 'echarts';
import 'echarts-wordcloud';
import 'echarts-extension-amap';
import ajax1 from './ajax1';
import {
    options,
} from "./CityScoreData.js";
export default {
    name: "cityExamination",
    data() {
        return {
            options: options,
            cityValue: [],
            city: "",
            wordcloudData: [{
                name: "韧性城市敏感词",
                value: 0,
            }],
            wordcloud: Object,
        };
    },

    mounted() {
        this.initWordCloud();
        this.wordCloudResetOptions();
        this.initResilienceBlock();
    },

    methods: {
        async recordCity() {
            this.city = this.cityValue[1];
            const cityData = await ajax1(`/evalution/getCityToughnessEvaluation/${this.city}`);
            if (cityData["data"] !== null) {
                this.initWordcloudData(cityData["data"]);
                this.wordCloudResetOptions();
            }
            else {
                alert("此城市尚未进行标准评分，请切换至上一栏菜单");
                this.city = "";
            }
        },

        initWordcloudData(data) {
            this.wordcloudData = [];
            for (let d of data) {
                if (d.val < 3) {
                    this.wordcloudData.push({
                        name: d.target,
                        value: (5 - d.val) * 8,
                    })
                }
            }
        },

        initWordCloud() {
            // 获取到ref绑定为loginTimes的DOM节点，以canvas的形式展现在视图层
            this.wordcloud = echarts.init(document.getElementById('roughnessWordCloud'));
        },

        wordCloudResetOptions() {
            // echarts参数设置
            this.wordcloud.setOption({
                backgroundColor: '#fff', // canvas背景颜色
                // canvas标题配置项
                series: [{
                    type: 'wordCloud',
                    shape: 'circle',
                    left: '0%',                  // X轴偏移量
                    top: '0%',                   // Y轴偏移量
                    width: '100%',               // canvas宽度大小
                    height: '100%',              // canvas高度大小
                    sizeRange: [24, 32],         //  词典字体大小范围配置
                    rotationRange: [0, 0],    // 词典字体旋转角度配置，默认不旋转
                    gridSize: 25,                // 词典字体间距配置
                    drawOutOfBound: false,       // 允许词太大的时候，超出画布的范围
                    layoutAnimation: true,       // 为false词典过度会阻塞
                    textStyle: {                 // 词典样式配置
                        color: function () {
                            // Random color
                            return 'rgb(' + [
                                Math.round(Math.random() * 160),
                                Math.round(Math.random() * 160),
                                Math.round(Math.random() * 160)
                            ].join(',') + ')';
                        }
                    },
                    // 渲染词典数据
                    data: this.wordcloudData,
                }]
            });
        },

        initResilienceBlock() {
            
        },
    },
};
</script>
  
<style lang="scss" scoped>

</style>  