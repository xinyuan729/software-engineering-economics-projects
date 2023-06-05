<template>
  <div class="app-container">
    <el-row :gutter="10">
      <el-drawer title="模拟列表" :visible.sync="moniListDrawer" :direction="moniDrawerDirection" size=580px :modal="false">
        <el-table :data="moniListData" style="width: 100%">
          <el-table-column fixed prop="id" label="序号" width="80">
          </el-table-column>
          <el-table-column prop="address" label="位置" width="80">
          </el-table-column>
          <el-table-column prop="type" label="任务类型" width="80">
          </el-table-column>
          <el-table-column prop="time" label="时间" width="180">
          </el-table-column>
          <el-table-column prop="status" label="状态" width="60">
            <template slot-scope="scope">
              <i class="el-icon-loading" v-if="scope.row.status === 'processing'"></i>
              <i class="el-icon-success" v-if="scope.row.status === 'finish'" style="color: green"></i>
            </template>
          </el-table-column>
          <el-table-column label="操作" width="100">
            <template slot-scope="scope">
              <el-button type="text" size="small" :disabled="isAvailable(scope.row)"
                @click="compute(scope.row)">导入计算</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-drawer>
      <el-col :span="9">
        <el-card style="height: 520px;">
          <div slot="header">
            <span>韧性城市模拟评分</span>
            <div style="position: relative; float: right; bottom: 10px;">
              <el-cascader :options="options" placeholder="城市" :show-all-levels="false" v-model="cityValue"
                @change="recordCity" style="width: 200px">
                <template slot-scope="{ node, data }">
                  <span>{{ data.label }}</span>
                  <span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
                </template>
              </el-cascader>
            </div>
          </div>
          <span>柱状图</span>
          <el-divider></el-divider>
          <div id="column" style="height: 320px; width: 500px; position: relative; float: left; top:20px"></div>
        </el-card>
        <el-card style="
                position: relative;
                background: #8fb8e0;
                top: 10px;
              ">
          <i class="score-item">生命线系统：</i><i style="float: right" class="score-item">
            {{ this.cityScore[0] }} 分</i><br />
          <i class="score-item">重要建筑物： </i><i style="float: right" class="score-item">
            {{ this.cityScore[1] }} 分</i><br />
          <i class="score-item">连接系统：</i><i style="float: right" class="score-item">
            {{ this.cityScore[2] }} 分</i><br />
          <i class="score-item">医疗服务系统：</i><i style="float: right" class="score-item">
            {{ this.cityScore[3] }} 分</i><br />
          <i class="score-item">污染处理系统：</i><i style="float: right" class="score-item">
            {{ this.cityScore[4] }} 分</i><br />
          <i class="score-item">开放空间系统：</i><i style="float: right" class="score-item">
            {{ this.cityScore[5] }} 分</i><br />
          <i class="score-item">其他：</i><i style="float: right" class="score-item">
            {{ this.cityScore[6] }} 分</i>
        </el-card>
      </el-col>
      <el-col :span="15">
        <el-card>
          <div slot="header">
            <el-row :gutter="10">
              <el-col :span="15">
                模拟评分细则（模拟数据评分与现状数据评分对比）
                <el-tooltip class="item" effect="light" :content="valueDetails" placement="right-start">
                  <i class="el-icon-warning-outline"></i>
                </el-tooltip>
              </el-col>
              <el-col :span="3">
                <el-upload ref="upload" action="" accept=".zip" :auto-upload="false" :on-change="uploadSHP"
                  :show-file-list="false" disabled>
                  <el-button type="warning" round>SHP导入</el-button>
                </el-upload>
              </el-col>
              <el-col :span="3"> <el-upload ref="upload" action="" accept=".xlsx, .xls" :auto-upload="false"
                  :on-change="uploadFoundData" :show-file-list="false" multiple>
                  <el-button type="success" round>基础数据</el-button>
                </el-upload>
              </el-col>
              <el-col :span="3"> <el-button type="primary" @click="moniListClick" round>模拟列表
                </el-button>
              </el-col>
            </el-row>
          </div>
          <div style="width: 100%; overflow-y: scroll; height: 670px">
            <table border="2" border-collapse="collapse" id="planningValuedTable">
              <thead style="position: sticky; top: 0; z-index: 2; background: #6cadee;">
                <tr>
                  <th style="width: 120px">系统</th>
                  <th style="width: 180px">指标</th>
                  <th style="width: 150px">模拟项</th>
                  <th style="width: 700px">人工评分</th>
                  <th style="width: 150px">现状评分</th>
                  <th style="width: 150px">模拟评分</th>
                  <th style="width: 150px">最终得分</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <th>{{ tableData[0][0].system }}</th>
                  <th>{{ tableData[0][0].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    水源地规划在数量、规模和连通度上
                    <el-cascader v-model="scoreValue[0][0]" :options="Poptions[0][0][0]"
                      @change="PlanningHandleChange(scoreValue[0][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[0][0] }} </th>
                  <th> {{ planValue[0][0] }} </th>
                  <th> {{ tableData[0][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[0][1].system }}</th>
                  <th>{{ tableData[0][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划能源设施在数量、总负荷上
                    <el-cascader v-model="scoreValue[0][1]" :options="Poptions[0][1][0]"
                      @change="PlanningHandleChange(scoreValue[0][1])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[0][1] }} </th>
                  <th> {{ planValue[0][1] }} </th>
                  <th> {{ tableData[0][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[0][2].system }}</th>
                  <th>{{ tableData[0][2].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划储备率和覆盖人口比例
                    <el-cascader v-model="scoreValue[0][2]" :options="Poptions[0][2][0]"
                      @change="PlanningHandleChange(scoreValue[0][2])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[0][2] }} </th>
                  <th> {{ planValue[0][2] }} </th>
                  <th> {{ tableData[0][2].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[0][3].system }}</th>
                  <th>{{ tableData[0][3].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    重要生命线工程设计设防标准
                    <el-cascader v-model="scoreValue[0][3]" :options="Poptions[0][3][0]"
                      @change="PlanningHandleChange(scoreValue[0][3])" style="width: 150px"></el-cascader>
                    《城市综合防灾规划标准》（GB/T51327-2018）要求
                  </th>
                  <th> {{ currentValue[0][3] }} </th>
                  <th> {{ planValue[0][3] }} </th>
                  <th> {{ tableData[0][3].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[0][4].system }}</th>
                  <th>{{ tableData[0][4].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划覆盖率
                    <el-cascader v-model="scoreValue[0][4]" :options="Poptions[0][4][0]"
                      @change="PlanningHandleChange(scoreValue[0][4])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[0][4] }} </th>
                  <th> {{ planValue[0][4] }} </th>
                  <th> {{ tableData[0][4].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[1][0].system }}</th>
                  <th>{{ tableData[1][0].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划比例
                    <el-cascader v-model="scoreValue[1][0]" :options="Poptions[1][0][0]"
                      @change="PlanningHandleChange(scoreValue[1][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[1][0] }} </th>
                  <th> {{ planValue[1][0] }} </th>
                  <th> {{ tableData[1][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[1][1].system }}</th>
                  <th>{{ tableData[1][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    重要建筑物设计设防标准
                    <el-cascader v-model="scoreValue[1][1]" :options="Poptions[1][1][0]"
                      @change="PlanningHandleChange(scoreValue[1][1])" style="width: 150px"></el-cascader>
                    《城市综合防灾规划标准》（GB/T51327-2018）要求
                  </th>
                  <th> {{ currentValue[1][1] }} </th>
                  <th> {{ planValue[1][1] }} </th>
                  <th> {{ tableData[1][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[1][2].system }}</th>
                  <th>{{ tableData[1][2].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划比例
                    <el-cascader v-model="scoreValue[1][2]" :options="Poptions[1][2][0]"
                      @change="PlanningHandleChange(scoreValue[1][2])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[1][2] }} </th>
                  <th>{{ planValue[1][2] }} </th>
                  <th> {{ tableData[1][2].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[1][3].system }}</th>
                  <th>{{ tableData[1][3].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划数量
                    <el-cascader v-model="scoreValue[1][3]" :options="Poptions[1][3][0]"
                      @change="PlanningHandleChange(scoreValue[1][3])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[1][3] }} </th>
                  <th> {{ planValue[1][3] }} </th>
                  <th> {{ tableData[1][3].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[1][4].system }}</th>
                  <th>{{ tableData[1][4].target }}</th>
                  <th style="color: green;">可模拟</th>
                  <th>
                    规划人均避难面积
                    <el-cascader v-model="scoreValue[1][4]" :options="Poptions[1][4][0]"
                      @change="PlanningHandleChange(scoreValue[1][4])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[1][4] }} </th>
                  <th> {{ planValue[1][4] }} </th>
                  <th> {{ tableData[1][4].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[1][5].system }}</th>
                  <th>{{ tableData[1][5].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划覆盖率
                    <el-cascader v-model="scoreValue[1][5]" :options="Poptions[1][5][0]"
                      @change="PlanningHandleChange(scoreValue[1][5])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[1][5] }} </th>
                  <th>{{ planValue[1][5] }} </th>
                  <th> {{ tableData[1][5].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[2][0].system }}</th>
                  <th>{{ tableData[2][0].target }}</th>
                  <th style="color: green;">可模拟</th>
                  <th>
                    规划比例
                    <el-cascader v-model="scoreValue[2][0]" :options="Poptions[2][0][0]"
                      @change="PlanningHandleChange(scoreValue[2][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[2][0] }} </th>
                  <th> {{ planValue[2][0] }} </th>
                  <th> {{ tableData[2][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[2][1].system }}</th>
                  <th>{{ tableData[2][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    重要交通基础设施计设防标准
                    <el-cascader v-model="scoreValue[2][1]" :options="Poptions[2][1][0]"
                      @change="PlanningHandleChange(scoreValue[2][1])" style="width: 150px"></el-cascader>
                    《城市综合防灾规划标准》（GB/T51327-2018）要求
                  </th>
                  <th> {{ currentValue[2][1] }} </th>
                  <th> {{ planValue[2][1] }} </th>
                  <th> {{ tableData[2][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[2][2].system }}</th>
                  <th>{{ tableData[2][2].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划
                    <el-cascader v-model="scoreValue[2][2]" :options="Poptions[2][2][0]"
                      @change="PlanningHandleChange(scoreValue[2][2])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[2][2] }} </th>
                  <th> {{ planValue[2][2] }} </th>
                  <th> {{ tableData[2][2].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[2][3].system }}</th>
                  <th>{{ tableData[2][3].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划长度
                    <el-cascader v-model="scoreValue[2][3]" :options="Poptions[2][3][0]"
                      @change="PlanningHandleChange(scoreValue[2][3])" style="width: 150px"></el-cascader>现状长度
                  </th>
                  <th> {{ currentValue[2][3] }} </th>
                  <th> {{ planValue[2][3] }} </th>
                  <th> {{ tableData[2][3].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[3][0].system }}</th>
                  <th>{{ tableData[3][0].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划用地面积
                    <el-cascader v-model="scoreValue[3][0]" :options="Poptions[3][0][0]"
                      @change="PlanningHandleChange(scoreValue[3][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[3][0] }} </th>
                  <th>{{ planValue[3][0] }}</th>
                  <th> {{ tableData[3][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[3][1].system }}</th>
                  <th>{{ tableData[3][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划覆盖率
                    <el-cascader v-model="scoreValue[3][1]" :options="Poptions[3][1][0]"
                      @change="PlanningHandleChange(scoreValue[3][1])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[3][1] }} </th>
                  <th> {{ planValue[3][1] }} </th>
                  <th> {{ tableData[3][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[3][2].system }}</th>
                  <th>{{ tableData[3][2].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划反应时间
                    <el-cascader v-model="scoreValue[3][2]" :options="Poptions[3][2][0]"
                      @change="PlanningHandleChange(scoreValue[3][2])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[3][2] }} </th>
                  <th> {{ planValue[3][2] }} </th>
                  <th> {{ tableData[3][2].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[4][0].system }}</th>
                  <th>{{ tableData[4][0].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划模块化布置程度
                    <el-cascader v-model="scoreValue[4][0]" :options="Poptions[4][0][0]"
                      @change="PlanningHandleChange(scoreValue[4][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[4][0] }} </th>
                  <th> {{ planValue[4][0] }} </th>
                  <th> {{ tableData[4][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[4][1].system }}</th>
                  <th>{{ tableData[4][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划排水系统覆盖率及雨污分流比例
                    <el-cascader v-model="scoreValue[4][1]" :options="Poptions[4][1][0]"
                      @change="PlanningHandleChange(scoreValue[4][1])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[4][1] }} </th>
                  <th> {{ planValue[4][1] }} </th>
                  <th> {{ tableData[4][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[5][0].system }}</th>
                  <th>{{ tableData[5][0].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划水面率
                    <el-cascader v-model="scoreValue[5][0]" :options="Poptions[5][0][0]"
                      @change="PlanningHandleChange(scoreValue[5][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[5][0] }} </th>
                  <th> {{ planValue[5][0] }} </th>
                  <th> {{ tableData[5][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[5][1].system }}</th>
                  <th>{{ tableData[5][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划面积占比
                    <el-cascader v-model="scoreValue[5][1]" :options="Poptions[5][1][0]"
                      @change="PlanningHandleChange(scoreValue[5][1])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[5][1] }} </th>
                  <th> {{ planValue[5][1] }} </th>
                  <th> {{ tableData[5][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[5][2].system }}</th>
                  <th>{{ tableData[5][2].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划绿地面积比例
                    <el-cascader v-model="scoreValue[5][2]" :options="Poptions[5][2][0]"
                      @change="PlanningHandleChange(scoreValue[5][2])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[5][2] }} </th>
                  <th> {{ planValue[5][2] }} </th>
                  <th> {{ tableData[5][2].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[5][3].system }}</th>
                  <th>{{ tableData[5][3].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划消纳率
                    <el-cascader v-model="scoreValue[5][3]" :options="Poptions[5][3][0]"
                      @change="PlanningHandleChange(scoreValue[5][3])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[5][3] }} </th>
                  <th> {{ planValue[5][3] }} </th>
                  <th> {{ tableData[5][3].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[6][0].system }}</th>
                  <th>{{ tableData[6][0].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    规划留白（储备）用地
                    <el-cascader v-model="scoreValue[6][0]" :options="Poptions[6][0][0]"
                      @change="PlanningHandleChange(scoreValue[6][0])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[6][0] }} </th>
                  <th> {{ planValue[6][0] }} </th>
                  <th> {{ tableData[6][0].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[6][1].system }}</th>
                  <th>{{ tableData[6][1].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>——</th>
                  <th> {{ currentValue[6][1] }} </th>
                  <th> {{ planValue[6][1] }} </th>
                  <th> {{ tableData[6][1].val }} </th>
                </tr>
                <tr>
                  <th>{{ tableData[6][2].system }}</th>
                  <th>{{ tableData[6][2].target }}</th>
                  <th style="color: red;">不可模拟</th>
                  <th>
                    面积比例
                    <el-cascader v-model="scoreValue[6][2]" :options="Poptions[6][2][0]"
                      @change="PlanningHandleChange(scoreValue[6][2])" style="width: 150px"></el-cascader>
                  </th>
                  <th> {{ currentValue[6][2] }} </th>
                  <th> {{ planValue[6][2] }} </th>
                  <th> {{ tableData[6][2].val }} </th>
                </tr>
              </tbody>
            </table>
          </div>
        </el-card>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import { Column } from "@antv/g2plot";
import FileSaver from "file-saver";
import xlsx from "xlsx";
import ajax1 from "./ajax1";
import { targetData, options, Poptions, moniData } from "./CityScoreData.js";
export default {
  name: "simulatorValue",
  data() {
    return {
      moniListDrawer: false,
      moniDrawerDirection: 'rtl',
      moniListData: [],
      cityfoundData: [],
      cityValue: [],
      scoreValue: [[], [], [], [], [], [], []],
      city: "",
      list: [],
      allhazardpoint: "",
      valueDetails: "",
      progress: 0,
      progressWord: "",
      tableData: targetData,
      options: options,
      Poptions: Poptions,
      moniData: moniData,
      currentValue: [[], [], [], [], [], [], []],
      planValue: [[], [], [], [], [], [], []],
      column: Object,
      radarAxis: 10,
      cityScore: [0, 0, 0, 0, 0, 0, 0],
      taskTypeNames: {
        'false': '模拟',
        'true': '推演',
      }
    };
  },
  props: {
    selectedCity: {
      people: {
        type: Number,
        default: () => { return 0 },
      },
      area: {
        type: Number,
        default: () => { return 0 },
      },
      dirtything: {
        type: Number,
        default: () => { return 0 },
      },
      type: Array,
      default: () => { return [] },
    },
    cityfoundDataFiles: {
      type: Array,
      default: () => { return [] },
    }
  },
  components: {},

  mounted() {
    this.cityValue = this.selectedCity;
    this.cityfoundData = this.cityfoundDataFiles;
    this.CityPeople = this.people;
    this.CityArea = this.area;
    this.AreaDirtyThing = this.dirtything;
    this.resetValue();
    //////////////////////////////////////////////////////////////////////////////////////////////////////////////////
    // 表格第一列合并
    let DOM = document.getElementById("planningValuedTable").tBodies[0].rows
    let spanArr = [{
      //合并第二列
      index: 0,
      rows: 0,     //0为第一列
    },];
    this.COMSpanArr(DOM, spanArr, 0);
    //////////////////////////////////////////////////////////////////////////////////////////////////////////////////

    this.valueDetails =
      "模拟评分：每项-1~1分，各系统总分高于0说明规划后模拟结果高于现状情况，等于0说明规划后模拟结构等于现状情况，低于0说明规划后模拟结果不及现状情况。";
    for (var i = 0; i < 7; i++) {
      for (var data of this.tableData[i]) {
        this.scoreValue[i].push("");
        this.currentValue[i].push(0);
        this.planValue[i].push(0);
      }
    }
    if (this.cityValue.length > 0) {
      this.recordCity();
    }
    this.ColumnInit();
  },

  methods: {
    resetValue: function () {
      // To do 浅复制问题笨解决
      this.scoreValue = [[], [], [], [], [], [], []];
      this.planValue = [[], [], [], [], [], [], []];
      this.moniListData = [];
      for (var i = 0; i < 7; i++) {
        for (var d of this.tableData[i]) {
          d.val = 0;
          this.scoreValue[i].push("");
          this.planValue[i].push(0);
        }
      }
    },

    async recordCity() {
      this.city = this.cityValue[1];
      const cityData = await ajax1(`/evalution/getCityToughnessEvaluation/${this.city}`);
      // console.log(cityData);
      if (cityData["data"] !== null) {
        this.clearScore();
        this.inputCurrentValue(cityData);
        this.inputMoniData();
      }
      else {
        this.$message({
          message: '此城市尚未进行标准评分，请切换至上一栏菜单',
          type: 'warning'
        });
        this.city = "";
      }
    },

    moniListClick: function () {
      console.log("update list");
      this.moniListDrawer = true;
      this.moniData = [];
      this.inputMoniData();
    },

    clearScore: function () {
      this.resetValue();
      this.rePrint();
    },

    rePrint: function () {
      this.cityScore = [0, 0, 0, 0, 0, 0, 0];
      for (var i = 0; i < 7; i++) {
        for (var data of this.tableData[i]) {
          this.cityScore[i] += data.val;
        }
      }
      this.column.destroy();
      this.ColumnInit();
    },

    uploadFoundData(file) {
      if (this.city === "") {
        this.$message({
          message: '请选择城市',
          type: 'warning'
        });
      } else {
        for (let i = 0; i < this.cityfoundData.length; i++) {
          let form = this.cityfoundData[i];
          if (form.name.slice(0, 6) === file.name.slice(0, 6)) {
            this.cityfoundData.splice(i, 1);
          }
        }
        this.cityfoundData.push(file);
      }
    },

    uploadSHP(file) {

    },

    inputCurrentValue: function (cityData) {
      let k = 0;
      for (let i = 0; i < 7; i++) {
        for (let j = 0; j < this.tableData[i].length; j++) {
          this.currentValue[i][j] = cityData["data"][k].val;
          k = k + 1;
        }
      };
      this.rePrint();
    },

    async inputMoniData() {
      let moniResult = await ajax1("/simulation/processes", "GET");
      console.log(moniResult);
      for (let i = 0; i < moniResult.length; i++) {
        if (moniResult[i].simulationArea === this.city) {
          this.moniListData.push({
            id: moniResult[i].id,
            address: this.parseCity(moniResult[i].simulationArea),
            type: this.taskTypeNames[moniResult[i].isNewSimulation],
            time: moniResult[i].simulationDate.slice(0, 10) + " " + moniResult[i].simulationDate.slice(11, 19),
            status: moniResult[i].simulationState
          });
        }
      }
    },

    isAvailable(row) {
      if (row.status === "finish") {
        return false;
      }
      else {
        return true;
      }
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

    compute(row) {
      for (let i = 0; i < this.cityfoundData.length; i++) {
        let form = this.cityfoundData[i];
        switch (form.name) {
          case "C0.xlsx":
            this.computeSimulator(form, row.id);
            break;
          case "C0.xls":
            this.computeSimulator(form, row.id);
            break;
        }
      }
    },

    async computeSimulator(ev, id) {
      if (this.city === "") {
        alert("请选择城市");
      } else {
        try {
          // 风险点数量
          let n = await ajax1(`/information/simulation/length/${id}/riskpoints/4`, "GET");
          // 第4时刻，0-4000个风险点
          let hazardpoint = await ajax1(`/information/simulation/subresult/${id}/riskpoints/4/0/4000`, "GET");
          let character = {
            location: {
              text: "location",
              type: "string",
            }
          };
          this.readExcel(ev, character).then((result) => {
            let transnum = result.length;
            let transdannum = 0;
            // 统计地区，a,b分别是灾害地经纬度
            for (var item1 of result) {
              var lc = eval('(' + item1.location + ')');
              var c = Number(lc.lng);
              var d = Number(lc.lat);
              for (var item of hazardpoint) {
                var geom = item.geom;
                let arr = geom.split(/[\s()]/);
                var a = Number(arr[2]);
                var b = Number(arr[3]);
                var count = this.hazard_compute(a, b, c, d);
                if (count === 1) {
                  transdannum = Number(transdannum) + 1;
                  break; // 为灾害建筑，跳出循环，避免重复计算
                }
              }
            }
            var ee = 0;
            var dd = Number(transdannum) / Number(transnum);
            if (dd >= 0.05) {
              ee = 1;
            } else if (dd >= 0.03 && dd < 0.05) {
              ee = 2;
            } else if (dd >= 0.02 && dd < 0.03) {
              ee = 3;
            } else if (dd >= 0.01 && dd < 0.02) {
              ee = 4;
            } else if (dd >= 0 && dd < 0.01) {
              ee = 5;
            } else {
              ee = 0;
            }
            this.planValue[2][0] = ee;
            if (this.currentValue[2][0] < this.planValue[2][0]) {
              this.tableData[2][0].val = 1;
            } else if (this.currentValue[2][0] === this.planValue[2][0]) {
              this.tableData[2][0].val = 0;
            } else {
              this.tableData[2][0].val = -1;
            }
            this.rePrint();
            this.$message({
              message: '灾害点数据导入成功,计算完成',
              type: 'success'
            });
          });
        }
        catch (err) {
          this.$message({
            message: '失败 ' + err,
            type: 'error'
          });
        }
      }
    },

    hazard_compute: function (a, b, c, d) {
      // a,b是灾害地经纬度，c,d是目标坐标的经纬度
      /*
      1、在纬度相等的情况下：
      经度每隔0.00001度，距离相差约1米；
      每隔0.0001度，距离相差约10米；
      每隔0.001度，距离相差约100米；
      每隔0.01度，距离相差约1000米；
      每隔0.1度，距离相差约10000米。
      2、在经度相等的情况下：
      纬度每隔0.00001度，距离相差约1.1米；
      每隔0.0001度，距离相差约11米；
      每隔0.001度，距离相差约111米；
      每隔0.01度，距离相差约1113米；
      每隔0.1度，距离相差约11132米。
      */
      // 相距小于50米属于灾害地
      if (
        ((a - c) / 0.0001 * 10) * ((a - c) / 0.0001 * 10) +
        ((b - d) / 0.0001 * 11) * ((b - d) / 0.0001 * 11) < 2500
      ) {
        return 1;
      } else {
        // 不属于灾害地
        return 0;
      }
    },

    readFile: function (file) {
      return new Promise((resolve) => {
        let reader = new FileReader();
        reader.readAsBinaryString(file);
        reader.onload = (ev) => {
          resolve(ev.target.result);
        };
      });
    },

    async readExcel(ev, character) {
      let file = ev.raw;
      if (!file) return;
      // 读取file中的数据,变成JSON格式
      let data = await this.readFile(file);
      let workbook = xlsx.read(data, { type: "binary" });
      let worksheet = workbook.Sheets[workbook.SheetNames[0]]; // 第一个sheet中的数据
      data = xlsx.utils.sheet_to_json(worksheet);
      // 把读取出来的数据变成可以传给服务器的数据
      let arr = [];
      data.forEach((item) => {
        let obj = {};
        for (let key in character) {
          // 数据
          if (!character.hasOwnProperty(key)) break;
          let v = character[key];
          let text = v.text;
          let type = v.type;
          v = item[text] || "";
          if (type === "number") {
            v = Number(v);
          }
          obj[key] = v;
        }
        arr.push(obj);
      });
      return arr;
    },

    PlanningHandleChange: function (value) {
      if (this.city === "") {
        this.$message({
          message: '请选择城市',
          type: 'warning'
        });
      } else {
        var arr = value[0].split("~");
        this.tableData[Number(arr[0] - 1)][Number(arr[1] - 1)].val =
          Number(arr[2]) - 2;
        this.rePrint();
      }
    },

    ColumnInit: function () {
      const data = [];

      for (var i = 0; i < 7; i++) {
        for (var d of this.tableData[i]) {
          data.push({
            name: d.target,
            system: d.system,
            value: d.val,
          });
        }
      }

      this.column = new Column("column", {
        data,
        isStack: true,
        xField: "system",
        yField: "value",
        yAxis: {
          max: 6,
          min: -6,
        },
        seriesField: "name",
        label: {
          // 可手动配置 label 数据标签位置
          position: "top", // 'top', 'bottom', 'middle'
          // 可配置附加的布局方法
          layout: [
            // 柱形图数据标签位置自动调整
            { type: "interval-adjust-position" },
            // 数据标签防遮挡
            { type: "interval-hide-overlap" },
            // 数据标签文颜色自动调整
            { type: "adjust-color" },
          ],
        },
        legend: false,
        // 使用 annotation （图形标注）来展示：总数的 label
      });
      this.column.render();
    },

    // 合并单元格
    // 来自CSDN 小白小党
    // 第一个参数为表格DOM
    // 第二个参数为数组[{index: 0,rows: 1,}]，index一般为0，rows为需要合并的列
    // 第三个参数为基准行

    COMSpanArr: function (DOM, array, standard) {
      if (DOM.length > 0) {
        for (var i = 0; i < DOM.length; i++) {
          if (i > 0) {
            for (var j = 0; j < array.length; j++) {
              if (DOM[array[j].index].cells[standard].innerText == DOM[i].cells[standard].innerText &&
                DOM[array[j].index].cells[array[j].rows].innerText == DOM[i].cells[array[j].rows].innerText) {
                DOM[i].cells[array[j].rows].style.display = "none"
                DOM[array[j].index].cells[array[j].rows].rowSpan = (DOM[array[j].index].cells[array[j].rows].rowSpan) + 1
              } else {
                array[j].index = i
              }
            }
          }
        }
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.score-item {
  font-size: 18px;
}
</style>
