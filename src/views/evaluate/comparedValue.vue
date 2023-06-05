<template>
  <div class="app-container">
    <el-row :gutter="10">
      <el-col :span="9">
        <el-card style="height: 700px;">
          <div slot="header">
            <span>韧性城市现状评分对比</span>
            <div style="position: relative; float: right; bottom: 10px;">
              <el-cascader :options="options" :props="props" style="width: 300px" placeholder="城市"
                :show-all-levels="false" v-model="cityValue" @change="changeCity">
                <template slot-scope="{ node, data }">
                  <span>{{ data.label }}</span>
                  <span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
                </template>
              </el-cascader>
            </div>
          </div>
          <span>雷达图</span>
          <el-divider></el-divider>
          <div id="compareRadar" style="
              height: 500px;
              width: 500px;
              position: relative;
              float: left;
            "></div>
        </el-card>
      </el-col>
      <el-col :span="15">
        <el-card>
          <div slot="header">
            <span>现状评分对比细则（不同城市现状评分对比）
              <el-tooltip class="item" effect="light" :content="valueDetails" placement="right-start">
                <i class="el-icon-warning-outline" style="position: relative; left: 5px; top: 0px"></i>
              </el-tooltip>
              <el-button type="success" style="position: relative; float: right; bottom: 10px;" @click="outputResult()"
                round>导出结果</el-button>
              <!-- <el-button type="warning" style="position: relative; float: right; right: 10px; bottom: 10px;"
                @click="clearScore()" round>清除评分
              </el-button> -->
            </span>
          </div>
          <el-table :data="tableData" border max-height="670" style="width: 100%; font-size: 15px" id="comparedtable">
            <el-table-column prop="system" label="系统" width="120">
            </el-table-column>
            <el-table-column prop="target" label="指标" width="350">
            </el-table-column>
            <el-table-column v-for="(detail, index) in this.compareDetails" :key="index" :label="detail.city"
              width="120">
              <template slot-scope="scope">
                <span> {{ loadScore(scope, detail.score) }} </span>
              </template>
            </el-table-column>
          </el-table>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { Radar } from "@antv/g2plot";
import { targetData, COMPRoptions, moniData } from "./CityScoreData.js";
import FileSaver from "file-saver";
import XLSX from "xlsx";
export default {
  name: "comparedValue",
  data() {
    return {
      cityValue: [],
      tableData: [],
      moniData: moniData,
      options: COMPRoptions,
      valueDetails: "现状评分：每项1~5分，分数越高，城市韧性越强。",
      props: { multiple: true },
      radarPlot: Object,
      radarAxis: 10,
      defaultScore: [0, 0, 0, 0, 0, 0, 0],
      compareCity: [],
      compareCityScore: [],
      compareDetails: [],
    };
  },
  components: {},

  mounted() {
    for (var data of targetData) {
      for (var t of data) {
        this.tableData.push({ system: t.system, target: t.target });
      }
    }
    // console.log(this.tableData);
    this.radarAxis = 10;
    this.compareRadarInit();
  },

  methods: {
    clearScore: function () {

    },

    outputResult: function () {
      if (this.city === "") {
        alert("请选择城市");
      } else {
        // 设置当前日期
        let time = new Date();
        let year = time.getFullYear();
        let month = time.getMonth() + 1;
        let day = time.getDate();
        let name = year + "" + month + "" + day + "对比结果";
        // console.log(name)
        /* generate workbook object from table */
        //  .table要导出的是哪一个表格
        var wb = XLSX.utils.table_to_book(document.getElementById('comparedtable'));
        this.$message({
          message: '导出成功',
          type: 'success'
        });
        /* get binary string as output */
        var wbout = XLSX.write(wb,
          {
            bookType: "xlsx",
            bookSST: true,
            type: "array"
          });
        try {
          FileSaver.saveAs(new Blob([wbout], { type: "application/octet-stream" }), name + ".xlsx");
        } catch (e) {
          if (typeof console !== "undefined") console.log(e, wbout);
        }
        return wbout;
      }
    },

    // 让每行显示对应数组的分数值
    loadScore: function (scope, arr) {
      return arr[scope.$index];
    },

    changeCity: function () {
      console.log(this.cityValue);
      this.compareCity = [];
      this.compareCityScore = [];
      this.compareDetails = [];
      this.radarAxis = 10;
      for (var arr of this.cityValue) {
        if (this.compareCity.indexOf(arr[1]) === -1) {
          this.compareCity.push(arr[1]);
        }
      }
      if (this.compareCity.length !== 0) {
        for (var city of this.compareCity) {
          let flag = false;
          for (var data of this.moniData) {
            if (city === data.city) {
              flag = true;
              this.compareCityScore.push(data.cityScore);
              let arr = [];
              for (var sys of data.details) {
                for (var ch of sys.children) {
                  arr.push(ch.value);
                }
              }
              this.compareDetails.push({ city: city, score: arr });
            }
          }
          if (!flag) {
            this.$message({
              message: city + '还没有进行现状评分',
              type: 'warning'
            });
          }
          else {
            this.radarAxis =
              (Math.floor(Math.max(...this.compareCityScore[0]) / 10) + 1) * 10;
          }
        }
      }
      this.radarPlot.destroy();
      this.compareRadarInit();
    },

    compareRadarInit: function () {
      const data = [];
      if (this.compareCity.length === 0) {
        data.push(
          { city: "城市", system: "生命线系统", score: this.defaultScore[0] },
          { city: "城市", system: "重要建筑物", score: this.defaultScore[1] },
          { city: "城市", system: "连接系统", score: this.defaultScore[2] },
          { city: "城市", system: "医疗服务系统", score: this.defaultScore[3] },
          { city: "城市", system: "污染处理系统", score: this.defaultScore[4] },
          { city: "城市", system: "开放空间系统", score: this.defaultScore[5] },
          { city: "城市", system: "其他", score: this.defaultScore[6] }
        );
      } else {
        for (var i = 0; i < this.compareCity.length; i++) {
          data.push(
            {
              city: this.compareCity[i],
              system: "生命线系统",
              score: this.compareCityScore[i][0],
            },
            {
              city: this.compareCity[i],
              system: "重要建筑物",
              score: this.compareCityScore[i][1],
            },
            {
              city: this.compareCity[i],
              system: "连接系统",
              score: this.compareCityScore[i][2],
            },
            {
              city: this.compareCity[i],
              system: "医疗服务系统",
              score: this.compareCityScore[i][3],
            },
            {
              city: this.compareCity[i],
              system: "污染处理系统",
              score: this.compareCityScore[i][4],
            },
            {
              city: this.compareCity[i],
              system: "开放空间系统",
              score: this.compareCityScore[i][5],
            },
            {
              city: this.compareCity[i],
              system: "其他",
              score: this.compareCityScore[i][6],
            }
          );
        }
      }

      this.radarPlot = new Radar("compareRadar", {
        data,
        autoFit: false,
        xField: "system",
        yField: "score",
        seriesField: "city",
        meta: {
          score: {
            alias: "分数",
          },
        },
        yAxis: {
          min: 0,
          max: this.radarAxis,
        },
        point: {},
        area: {},
      });
      this.radarPlot.render();
    },
  },
};
</script>

<style lang="scss" scoped>

</style>
