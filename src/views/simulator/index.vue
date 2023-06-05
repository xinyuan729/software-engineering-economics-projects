<template>
  <div class="app-container">
    <el-row :gutter="32">
      <el-col :span="8">
        <el-card>
          <div slot="header">
            <span>参数设置</span>
          </div>
          <el-form ref="form">
            <el-form-item label="灾害类型:">
              <el-radio v-model="type" label="flood">暴雨</el-radio>
              <el-radio v-model="type" label="infection">疫情</el-radio>
            </el-form-item>

            <!-- 设置洪水数据 -->
            <div v-show="type == 'flood'">
              <el-form-item label="模拟区域:">
                <el-cascader :options="areaOptions" placeholder="城市" :show-all-levels="false" v-model="area">
                  <template slot-scope="{ node, data }">
                    <span>{{ data.label }}</span>
                    <span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
                  </template>
                </el-cascader>
              </el-form-item>
              <el-form-item label="水文数据:">
                <el-popover placement="right" width="800" trigger="hover">
                  <p class="helpTitle">数据要求</p>
                  <el-image :src="helpImgs['water']"></el-image>
                  <i class="el-icon-question" slot="reference"></i>
                </el-popover>
                <el-upload name="waters" :on-change="uploadWaterFiles" :auto-upload="false" multiple action=""
                  :file-list="waterFiles">
                  <i class="el-icon-upload"></i>
                  <div class="el-upload__tip" slot="tip">
                    请上传waters.cpg文件、waters.dbf文件、waters.prj文件、waters.qix文件、waters.sbn文件、waters.sbx文件、waters.shp文件、waters.shp文件(xml)、waters.shx文件
                  </div>
                </el-upload>
              </el-form-item>
              <el-form-item label="路网数据:">
                <el-popover placement="right" width="800" trigger="hover">
                  <p class="helpTitle">数据要求</p>
                  <el-image :src="helpImgs['road']"></el-image>
                  <i class="el-icon-question" slot="reference"></i>
                </el-popover>
                <el-upload name="roads" :auto-upload="false" :on-change="uploadRoadFiles" multiple action=""
                  :file-list="roadFiles">
                  <i class="el-icon-upload"></i>
                  <div class="el-upload__tip" slot="tip">
                    请上传roads.cpg文件、roads.dbf文件、roads.prj文件、roads.qix文件、roads.sbn文件、roads.sbx文件、roads.shp文件、roads.shp文件(xml)、roads.shx文件
                  </div>
                </el-upload>
              </el-form-item>
              <el-form-item label="建筑物数据:">
                <el-popover placement="right" width="800" trigger="hover">
                  <p class="helpTitle">数据要求</p>
                  <el-image :src="helpImgs['building']"></el-image>
                  <i class="el-icon-question" slot="reference"></i>
                </el-popover>
                <el-upload name="buildings" :auto-upload="false" :on-change="uploadBuildingFiles" multiple action=""
                  :file-list="buildingFiles">
                  <i class="el-icon-upload"></i>
                  <div class="el-upload__tip" slot="tip">
                    请上传buildings.cpg文件、buildings.dbf文件、buildings.prj文件、buildings.qix文件、buildings.sbn文件、buildings.sbx文件、buildings.shp文件、buildings.shp文件(xml)、buildings.shx文件
                  </div>
                </el-upload>
              </el-form-item>
              <el-form-item label="高程数据:">
                <el-popover placement="right" width="800" trigger="hover">
                  <p class="helpTitle">数据要求</p>
                  <el-image :src="helpImgs['dem']"></el-image>
                  <p style="text-align:center; font-size: large; margin: 0;">栅格总数 &lt; 650万</p>
                  <i class="el-icon-question" slot="reference"></i>
                </el-popover>
                <el-upload name="dem" :auto-upload="false" :on-change="uploadDemFiles" multiple action=""
                  :file-list="demFiles">
                  <i class="el-icon-upload"></i>
                  <div class="el-upload__tip" slot="tip">
                    请上传dem.ovr文件、dem.tfw文件、dem.tif文件、dem.xml文件
                  </div>
                </el-upload>
              </el-form-item>
              <el-form-item label="交通出行量(OD)数据:">
                <el-popover placement="right" width="800" trigger="hover">
                  <p class="helpTitle">数据要求</p>
                  <el-image :src="helpImgs['od']"></el-image>
                  <i class="el-icon-question" slot="reference"></i>
                </el-popover>
                <el-upload name="od" :on-change="uploadODFile" :auto-upload="false" multiple action=""
                  :file-list="odFiles">
                  <i class="el-icon-upload"></i>
                  <div class="el-upload__tip" slot="tip">
                    请上传od.csv文件
                  </div>
                </el-upload>
              </el-form-item>
              <el-form-item label="初始降雨中心点X坐标(InitX, 0～1):">
                <el-input v-model="floodParam.InitX" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item label="初始降雨中心点Y坐标(InitY, 0～1):">
                <el-input v-model="floodParam.InitY" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item label="降雨半径(Radius, 0～1):">
                <el-input v-model="floodParam.Radius" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item label="移动方向(Movement direction, 0～360):">
                <el-input v-model="floodParam['Movement direction']" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item label="移动速度(Movement speed, 0～1):">
                <el-input v-model="floodParam['Movement speed']" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item label="每小时降雨量(Rainfall per hour, 0～1):">
                <el-input v-model="floodParam['Rainfall per hour']" placeholder="请输入内容"></el-input>
              </el-form-item>
              <el-form-item label="水流扩散速率(Diffusion rate, 0～1):">
                <el-input v-model="floodParam['Diffusion rate']" placeholder="请输入内容"></el-input>
              </el-form-item>
            </div>

            <!-- 设置疫情数据 -->
            <div v-show="type == 'infection'">
              <el-form-item label="模拟方式:">
                <el-radio v-model="infectionModel" label="seir">SEIR模拟</el-radio>
                <el-radio v-model="infectionModel" label="gamma">传染病空间模拟</el-radio>
                <el-radio v-model="infectionModel" label="maddpg">多智能体强化学习模拟</el-radio>
              </el-form-item>
              <!-- 设置SEIR数据 -->
              <div v-show="infectionModel == 'seir'">
                <el-form-item label="模拟区域:">
                  <el-select v-model="infectionParamSeir['simulation_area']" placeholder="请选择">
                    <el-option v-for="item in areaOptions_seir" :key="item.value" :label="item.label" :value="item.value">
                    </el-option>
                  </el-select>
                </el-form-item>
                <el-form-item label="隔离开始日期:">
                  <el-input v-model="infectionParamSeir['isolation_start']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="隔离结束日期:">
                  <el-input v-model="infectionParamSeir['isolation_end']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="模拟开始时间:">
                  <el-input v-model="infectionParamSeir['start_time']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="模拟结束时间:">
                  <el-input v-model="infectionParamSeir['end_time']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-collapse>
                  <el-collapse-item>
                    <template slot="title">
                      <strong>更多设置</strong>
                    </template>
                    <el-form-item label="社群网络内节点度数:">
                      <el-input v-model="infectionParamSeir['MEAN_INTRACOHORT_DEGREE']" placeholder="请输入内容"></el-input>
                    </el-form-item>
                    <el-form-item label="社群网络接触后感染可能性:">
                      <el-input v-model="infectionParamSeir['PCT_CONTACTS_INTERCOHORT']" placeholder="请输入内容"></el-input>
                    </el-form-item>
                    <el-form-item label="初始感染节点数:">
                      <el-input v-model="infectionParamSeir['INIT_EXPOSED']" placeholder="请输入内容"></el-input>
                    </el-form-item>
                    <el-form-item label="基本传染数:">
                      <span slot="label">
                        <span>基本传染数:</span>
                        <!-- <el-tooltip effect="dark" content="平均每位感染者在传染期内使易感者个体致病的数量" placement="right">
                          <i class="el-icon-question el-icon--right"></i> 
                        </el-tooltip> -->
                      </span>
                      <el-input v-model="infectionParamSeir['R0']" placeholder="请输入内容"></el-input>
                    </el-form-item>
                  </el-collapse-item>
                </el-collapse>
              </div>
              <!-- 设置Gamma数据 -->
              <div v-show="infectionModel == 'gamma'">
                <el-form-item label="模拟区域:">
                  <el-cascader :options="areaOptions" placeholder="城市" :show-all-levels="false" v-model="area">
                    <template slot-scope="{ node, data }">
                      <span>{{ data.label }}</span>
                      <span v-if="!node.isLeaf"> ({{ data.children.length }}) </span>
                    </template>
                  </el-cascader>
                </el-form-item>
                <el-form-item label="路网数据:">
                  <el-popover placement="right" width="800" trigger="hover">
                    <p class="helpTitle">数据要求</p>
                    <el-image :src="helpImgs['road']"></el-image>
                    <i class="el-icon-question" slot="reference"></i>
                  </el-popover>
                  <el-upload name="roads" :auto-upload="false" :on-change="uploadRoadFiles" multiple action=""
                    :file-list="roadFiles">
                    <i class="el-icon-upload"></i>
                    <div class="el-upload__tip" slot="tip">
                      请上传roads.cpg文件、roads.dbf文件、roads.prj文件、roads.qix文件、roads.sbn文件、roads.sbx文件、roads.shp文件、roads.shp文件(xml)、roads.shx文件
                    </div>
                  </el-upload>
                </el-form-item>
                <el-form-item label="建筑物数据:">
                  <el-popover placement="right" width="800" trigger="hover">
                    <p class="helpTitle">数据要求</p>
                    <el-image :src="helpImgs['building']"></el-image>
                    <i class="el-icon-question" slot="reference"></i>
                  </el-popover>
                  <el-upload name="buildings" :auto-upload="false" :on-change="uploadBuildingFiles" multiple action=""
                    :file-list="buildingFiles">
                    <i class="el-icon-upload"></i>
                    <div class="el-upload__tip" slot="tip">
                      请上传buildings.cpg文件、buildings.dbf文件、buildings.prj文件、buildings.qix文件、buildings.sbn文件、buildings.sbx文件、buildings.shp文件、buildings.shp文件(xml)、buildings.shx文件
                    </div>
                  </el-upload>
                </el-form-item>
                <el-form-item label="高程数据:">
                  <el-popover placement="right" width="800" trigger="hover">
                    <p class="helpTitle">数据要求</p>
                    <el-image :src="helpImgs['dem']"></el-image>
                    <p style="text-align:center; font-size: large; margin: 0;">栅格总数 &lt; 650万</p>
                    <i class="el-icon-question" slot="reference"></i>
                  </el-popover>
                  <el-upload name="dem" :auto-upload="false" :on-change="uploadDemFiles" multiple action=""
                    :file-list="demFiles">
                    <i class="el-icon-upload"></i>
                    <div class="el-upload__tip" slot="tip">
                      请上传dem.ovr文件、dem.tfw文件、dem.tif文件、dem.xml文件
                    </div>
                  </el-upload>
                </el-form-item>

                <el-form-item label="人口总数(Population):" v-show="type == 'infection'">
                  <el-input v-model="infectionParamGamma.Population" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="初始感染人数(Initial Infection):" v-show="type == 'infection'">
                  <el-input v-model="infectionParamGamma['Initial Infection']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="人群移速(Crowd Speed, 千米/小时):" v-show="type == 'infection'">
                  <el-input v-model="infectionParamGamma['Crowd Speed']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="可感染范围(Infectious Range, 米):" v-show="type == 'infection'">
                  <el-input v-model="infectionParamGamma['Infectious Range']" placeholder="请输入内容"></el-input>
                </el-form-item>
              </div>
              <!-- 设置MADDPG数据 -->
              <div v-show="infectionModel == 'maddpg'">
                <el-form-item label="区域:">
                  <el-input v-model="infectionParamMADDPG['Region']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="传染病基本繁殖数:">
                  <el-input v-model="infectionParamMADDPG['R0']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="住院率:">
                  <el-input v-model="infectionParamMADDPG['Gamma']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="治愈率:">
                  <el-input v-model="infectionParamMADDPG['Theta']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="初始感染人数:">
                  <el-input v-model="infectionParamMADDPG['Number']" placeholder="请输入内容"></el-input>
                </el-form-item>
                <el-form-item label="行政区域划分数据:">
                  <el-upload name="regions" :auto-upload="false" :on-change="uploadRegionFiles" action="">
                    <i class="el-icon-upload"></i>
                    <div class="el-upload__tip" slot="tip">
                      请上传shp文件
                    </div>
                  </el-upload>
                </el-form-item>
                <el-form-item label="人口数量数据:">
                  <el-upload name="population" :auto-upload="false" :on-change="uploadPopulationFile" action="">
                    <i class="el-icon-upload"></i>
                    <div class="el-upload__tip" slot="tip">
                      请上传population.npy文件
                    </div>
                  </el-upload>
                </el-form-item>
                <el-form-item label="人口出行量数据:">
                  <el-upload name="od" :auto-upload="false" :on-change="uploadODFileInfection" action="">
                    <i class="el-icon-upload"></i>
                    <div class="el-upload__tip" slot="tip">
                      请上传od.npy文件
                    </div>
                  </el-upload>
                </el-form-item>
              </div>
            </div>

            <!-- 操作 -->
            <el-form-item>
              <el-button @click="submitTask" type="primary">提交任务</el-button>
            </el-form-item>


          </el-form>
        </el-card>
      </el-col>
      <el-col :span="16">
        <el-table :data="tableData">
          <el-table-column label="序号" prop="id" :show-overflow-tooltip="true" width="80" />
          <!-- <el-table-column label="名称" prop="simulationArea" align="center" :show-overflow-tooltip="true">
            <template slot-scope="scope">
              <span>{{
                  scope.row.simulationArea + "-" + scope.row.simulationType
              }}</span>
            </template>
          </el-table-column> -->
          <el-table-column label="位置" prop="simulationArea" align="center" :show-overflow-tooltip="true">
            <template slot-scope="scope">
              <span>{{ parseCity(scope.row.simulationArea) }}</span>
            </template>
          </el-table-column>
          <el-table-column label="灾害类型" prop="simulationType" :show-overflow-tooltip="true" width="100">
            <template slot-scope="scope">
              <span>{{ typeNames[scope.row.simulationType] }}</span>
            </template>
          </el-table-column>

          <el-table-column label="任务类型" prop="isNewSimulation" :show-overflow-tooltip="true" width="100">
            <template slot-scope="scope">
              <span>{{ taskTypeNames[scope.row.isNewSimulation] }}</span>
            </template>
          </el-table-column>

          <el-table-column label="任务提交时间" align="center" width="180" prop="simulationDate">
            <template slot-scope="scope">
              <span>{{ scope.row.simulationDate }}</span>
            </template>
          </el-table-column>

          <el-table-column label="状态" align="center" width="100" prop="simulationDate">
            <template slot-scope="scope">
              <i class="el-icon-loading" v-if="scope.row.simulationState === 'processing'"></i>
              <i class="el-icon-success" v-if="scope.row.simulationState === 'finish'" style="color: green"></i>
            </template>
          </el-table-column>

          <el-table-column label="模拟计算时间" align="center" width="180" prop="simulationDate">
            <template slot-scope="scope">
              <span>{{ scope.row.simulationTime }}</span>
            </template>
          </el-table-column>

          <el-table-column label="操作" align="center" class-name="small-padding fixed-width">
            <template slot-scope="scope">
              <el-button size="mini" type="text" icon="el-icon-set-up" :disabled="scope.row.simulationState !== 'finish'"
                @click="clickTask(scope.row)">查看</el-button>
            </template>
          </el-table-column>
        </el-table>
      </el-col>
    </el-row>
    <el-drawer :title="drawType == 'flood' ? '暴雨灾害模拟结果' : '疫情模拟结果'" :visible.sync="drawer" size="78%">
      <flodsimulation :type="drawType" :status="status" :metric="metric" v-show="drawType == 'flood'" />
      <infectionSimulation :type="drawType" :infectionModel="infectionModel" :status="status"
        v-show="drawType == 'infection'" />
    </el-drawer>
  </div>
</template>

<script>
/* eslint-disable no-undef */
import axios from "axios";
import ajax from "./ajax";
import serverInfo from './serverInfo';
import flodsimulation from "./components/disasterSimulator-ol.vue";
import infectionSimulation from "./components/infectionSimulation-ol.vue";
import { options } from "../evaluate/CityScoreData.js";

export default {
  name: "Summary",
  data() {
    return {
      helpImgs: {
        "water": require("@/assets/images/dataRequirement/waterFiles.jpg"),
        "road": require("@/assets/images/dataRequirement/roadFiles.jpg"),
        "building": require("@/assets/images/dataRequirement/buildingFiles.jpg"),
        "dem": require("@/assets/images/dataRequirement/demFiles.jpg"),
        "od": require("@/assets/images/dataRequirement/odFiles.jpg"),
      },
      type: "flood",
      drawType: "flood",
      infectionModel: "seir",

      typeNames: {
        'flood': '暴雨',
        'infection': '疫情',
      },
      taskTypeNames: {
        'false': '模拟',
        'true': '推演',
      },

      area: [],
      areaOptions: options,

      areaOptions_seir: [
        {
          value: 'Shanghai',
          label: '上海',
        },
        {
          value: 'Minhang',
          label: '闵行',
        },
        {
          value: 'Yangpu',
          label: '杨浦',
        },
      ],

      files: [],
      floodParam: {
        InitX: "0.5",
        InitY: "0.2",
        Radius: "0.5",
        "Movement direction": "0.0",
        "Movement speed": "0.03",
        "Rainfall per hour": "0.02",
        "Diffusion rate": "0.6",
        type: "flood",
        "Whether drainage facilities are used": "false",
      },
      infectionParamSeir: {
        type: "infection",
        "simulation_area": "Minhang",
        "MEAN_INTRACOHORT_DEGREE": 9,
        "PCT_CONTACTS_INTERCOHORT": 0.3,
        "INIT_EXPOSED": 2,
        "isolation_start": "2022/04/01",
        "isolation_end": "2022/06/30",
        "start_time": "2022/03/19",
        "end_time": "2022/08/01",
        "R0": 4.7,
      },
      infectionParamGamma: {
        type: "infection",
        Population: "1000",
        "Initial Infection": "3",
        "Crowd Speed": "0.01",
        "Infectious Range": "0.01",
      },
      infectionParamMADDPG: {
        "R0": 1.6,
        "Gamma": 0.45,
        "Theta": 0.15,
        "Number": 10,
        "Region": "新华",
        "file": [],
        "populationFile": [],
        "odFile": [],
      },
      tableData: [],
      status: {},
      metric: {},
      drawer: false,
      waterFiles: [],
      roadFiles: [],
      buildingFiles: [],
      demFiles: [],
      odFiles: [],

      taskTimer: null,
    };
  },
  components: { flodsimulation, infectionSimulation },
  mounted() {
    let getTaskFunction = () => {
      this.getTaskList();
      console.log("Get Task List!");
    };
    getTaskFunction();
    // this.taskTimer = setInterval(getTaskFunction, 10000);
  },
  methods: {
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
    displayHelp(value) {
      console.log(value);
    },
    uploadWaterFiles(file) {
      let fileName = file.name;
      if (fileName.split(".")[0] != "waters") {
        setTimeout(() => {
          this.$message.error(`请将上传文件命名为waters.${fileName.split(".")[1]}`);
        }, 100);
        this.waterFiles = this.waterFiles.slice(0, -1);
        return;
      }
      else {
        this.waterFiles.push(file);
        this.files.push(file);
      }
    },
    uploadRoadFiles(file) {
      let fileName = file.name;
      if (fileName.split(".")[0] != "roads") {
        setTimeout(() => {
          this.$message.error(`请将上传文件命名为roads.${fileName.split(".")[1]}`);
        }, 100);
        this.roadFiles = this.roadFiles.slice(0, -1);
        return;
      }
      else {
        this.roadFiles.push(file);
        this.files.push(file);
      }
    },
    uploadBuildingFiles(file) {
      let fileName = file.name;
      if (fileName.split(".")[0] != "buildings") {
        setTimeout(() => {
          this.$message.error(`请将上传文件命名为buildings.${fileName.split(".")[1]}`);
        }, 100);
        this.buildingFiles = this.buildingFiles.slice(0, -1);
        return;
      }
      else {
        this.buildingFiles.push(file);
        this.files.push(file);
      }
    },
    uploadDemFiles(file) {
      let fileName = file.name;
      if (fileName.split(".")[0] != "dem") {
        setTimeout(() => {
          this.$message.error(`请将上传文件命名为dem.${fileName.split(".")[1]}`);
        }, 100);
        this.demFiles = this.demFiles.slice(0, -1);
        return;
      }
      else {
        this.demFiles.push(file);
        this.files.push(file);
      }
    },
    uploadODFile(file) {
      let fileName = file.name;
      if (fileName.split(".")[0] != "od") {
        this.$message.error(`请将上传文件命名为od.${fileName.split(".")[1]}`);
        this.odFiles = this.odFiles.slice(0, -1);
        return;
      }
      else {
        this.odFiles.push(file);
        this.files.push(file);
      }
    },
    uploadRegionFiles(file) {
      this.infectionParamMADDPG["file"].push(file);
    },
    uploadPopulationFile(file) {
      this.infectionParamMADDPG["populationFile"].push(file);
    },
    uploadODFileInfection(file) {
      this.infectionParamMADDPG["odFile"].push(file);
    },
    submitTask() {

      console.log(this.type, this.infectionModel);
      if (this.type === "flood") {
        if (this.area.length === 0)
          return this.$message.error("请选择模拟区域");
        if (this.waterFiles.length === 0)
          return this.$message.error("请上传水文数据");
        if (this.roadFiles.length === 0)
          return this.$message.error("请上传路网数据");
        if (this.buildingFiles.length === 0)
          return this.$message.error("请上传建筑物数据");
        if (this.demFiles.length === 0)
          return this.$message.error("请上传路网数据");

        let totalSize = 0;
        this.waterFiles.forEach((file) => {
          totalSize += file.size;
        });
        this.roadFiles.forEach((file) => {
          totalSize += file.size;
        });
        this.buildingFiles.forEach((file) => {
          totalSize += file.size;
        });
        this.demFiles.forEach((file) => {
          totalSize += file.size;
        });
        if (totalSize >= 1073741824) {
          this.$message.error("上传文件总大小不能超过1GB");
          return;
        }

        if (
          Number(this.floodParam["Movement direction"]) > 360 ||
          Number(this.floodParam["Movement direction"]) < 0
        ) {
          return this.$message.error("移动方向必须在0-360之间");
        }
        if (
          Number(this.floodParam["Diffusion rate"]) > 1 ||
          Number(this.floodParam["Diffusion rate"]) < 0
        ) {
          return this.$message.error("扩散速率必须在0-1之间");
        }
        if (
          Number(this.floodParam["InitX"]) > 1 ||
          Number(this.floodParam["InitX"]) < 0
        ) {
          return this.$message.error("初始降雨中心点X坐标必须在0-1之间");
        }
        if (
          Number(this.floodParam["InitY"]) > 1 ||
          Number(this.floodParam["InitY"]) < 0
        ) {
          return this.$message.error("初始降雨中心点Y坐标必须在0-1之间");
        }
        if (
          Number(this.floodParam["Radius"]) > 1 ||
          Number(this.floodParam["Radius"]) < 0
        ) {
          return this.$message.error("降雨半径必须在0-1之间");
        }
        if (
          Number(this.floodParam["Movement speed"]) > 1 ||
          Number(this.floodParam["Movement speed"]) < 0
        ) {
          return this.$message.error("移动速度必须在0-1之间");
        }
        if (Number(this.floodParam["Rainfall per hour"]) < 0) {
          return this.$message.error("每小时降雨量必须大于0");
        }

        const loading = this.$loading({
          lock: true,
          text: "仿真环境上传中",
          spinner: "el-icon-loading",
          background: "rgba(0, 0, 0, 0.7)",
        });

        let formData = new FormData();
        // formData重复的往一个值添加数据并不会被覆盖掉，可以全部接收到，可以通过formData.getAll('files')来查看所有插入的数据
        for (let i = 0; i < this.files.length; i++) {
          formData.append("files", this.files[i].raw);
        }
        formData.append("type", this.type);
        formData.append("area", this.area[1]);
        formData.append(
          "disturbance",
          JSON.stringify(
            this.type === "flood" ? this.floodParam : this.infectionParam
          )
        );
        let url = serverInfo.baseURL + "/simulation/simulator";
        let headers = {
          "Content-Type": "multipart/form-data",
        };
        axios
          .post(url, formData, { headers: headers })
          .then((res) => {
            loading.close();
            this.$message({
              message: "仿真环境上传成功",
              type: "success",
            });
            this.getTaskList(()=>{
              this.requestMeasure();
            });
          })
          .catch((err) => {
            console.log(err);
            loading.close();
            this.$message({
              message: "仿真环境上传失败",
              type: "error",
            });
          });


      } else {

        if (this.infectionModel === "seir") {

          if (Number(this.infectionParamSeir["MEAN_INTRACOHORT_DEGREE"]) < 0) {
            return this.$message.error("社群网络内节点度数必须大于0");
          }
          if (Number(this.infectionParamSeir["PCT_CONTACTS_INTERCOHORT"]) < 0) {
            return this.$message.error("社群网络接触后感染可能性必须大于0");
          }
          if (Number(this.infectionParamSeir["INIT_EXPOSED"]) < 0) {
            return this.$message.error("初始感染节点数必须大于0");
          }
          if (Number(this.infectionParamSeir["R0"]) < 0) {
            return this.$message.error("R0必须大于0");
          }

          const loading = this.$loading({
            lock: true,
            text: "仿真环境上传中",
            spinner: "el-icon-loading",
            background: "rgba(0, 0, 0, 0.7)",
          });

          // {
          //   loading.close();
          //   this.$message({
          //     message: "仿真环境上传成功",
          //     type: "success",
          //   });
          //   this.status = {
          //     imgData: 'http://124.223.72.207:8888/static/Infection/infectionResult.jpg',
          //     imgData_predict: 'http://124.223.72.207:8888/static/Infection/infectionResult_predict.png',
          //     computeTime: 30,
          //   };
          //   this.drawType = "infection";
          //   this.drawer = true;
          // }

          let headers = {
            "Content-Type": "application/json",
          };
          const service = axios.create({
            baseURL: serverInfo.baseURL_infection,
            timeout: 16000000
          });
          service
            .post('', this.infectionParamSeir, { headers: headers })
            .then((res) => {
              loading.close();
              this.$message({
                message: "仿真环境上传成功",
                type: "success",
              });
              this.status = {
                imgData: `data:image/jpg;base64,${res.data.img}`,
                imgData_predict: `data:image/jpg;base64,${res.data.img_predict}`,
              };
              this.drawType = "infection";
              this.drawer = true;
            })
            .catch((err) => {
              console.log(err);
              loading.close();
              this.$message({
                message: "仿真环境上传失败",
                type: "error",
              });
            });


        }
        else if (this.infectionModel === "gamma") {
          if (this.area.length === 0)
            return this.$message.error("请选择模拟区域");
          if (this.roadFiles.length === 0)
            return this.$message.error("请上传路网数据");
          if (this.buildingFiles.length === 0)
            return this.$message.error("请上传建筑物数据");
          if (this.demFiles.length === 0)
            return this.$message.error("请上传路网数据");

          let totalSize = 0;
          this.roadFiles.forEach((file) => {
            totalSize += file.size;
          });
          this.buildingFiles.forEach((file) => {
            totalSize += file.size;
          });
          this.demFiles.forEach((file) => {
            totalSize += file.size;
          });
          if (totalSize >= 1073741824) {
            this.$message.error("上传文件总大小不能超过1GB");
            return;
          }

          if (Number(this.infectionParamGamma["Population"]) < 0) {
            return this.$message.error("人口总数必须大于0");
          }
          if (Number(this.infectionParamGamma["Initial Infection"]) < 0) {
            return this.$message.error("初始感染人数必须大于0");
          }
          if (Number(this.infectionParamGamma["Crowd Speed"]) < 0) {
            return this.$message.error("人群移速必须大于0");
          }
          if (Number(this.infectionParamGamma["Infectious Range"]) < 0) {
            return this.$message.error("可感染范围必须大于0");
          }

          const loading = this.$loading({
            lock: true,
            text: "仿真环境上传中",
            spinner: "el-icon-loading",
            background: "rgba(0, 0, 0, 0.7)",
          });
          let formData = new FormData();
          // formData重复的往一个值添加数据并不会被覆盖掉，可以全部接收到，可以通过formData.getAll('files')来查看所有插入的数据
          for (let i = 0; i < this.files.length; i++) {
            formData.append("files", this.files[i].raw);
          }
          formData.append("type", this.type);
          formData.append("area", this.area[1]);
          formData.append(
            "disturbance",
            JSON.stringify(
              this.type === "flood" ? this.floodParam : this.infectionParamGamma
            )
          );
          let url = serverInfo.baseURL + "/simulation/simulator";
          let headers = {
            "Content-Type": "multipart/form-data",
          };
          axios
            .post(url, formData, { headers: headers })
            .then((res) => {
              loading.close();
              this.$message({
                message: "仿真环境上传成功",
                type: "success",
              });
              this.getTaskList();
            })
            .catch((err) => {
              console.log(err);
              loading.close();
              this.$message({
                message: "仿真环境上传失败",
                type: "error",
              });
            });


        }
        else if (this.infectionModel === "maddpg") {

          if (Number(this.infectionParamMADDPG["R0"]) < 0) {
            return this.$message.error("传染病基本繁殖数必须大于0");
          }
          if (Number(this.infectionParamMADDPG["Gamma"]) < 0 || Number(this.infectionParamMADDPG["Gamma"]) > 1) {
            return this.$message.error("住院率必须在0~1之间");
          }
          if (Number(this.infectionParamMADDPG["Theta"]) < 0 || Number(this.infectionParamMADDPG["Theta"]) > 1) {
            return this.$message.error("治愈率必须在0~1之间");
          }
          if (Number(this.infectionParamMADDPG["Num"]) < 0) {
            return this.$message.error("初始感染人数必须大于0");
          }

          const loading = this.$loading({
            lock: true,
            text: "仿真环境上传中",
            spinner: "el-icon-loading",
            background: "rgba(0, 0, 0, 0.7)",
          });

          // {
          //   loading.close();
          //   this.$message({
          //     message: "仿真环境上传成功",
          //     type: "success",
          //   });
          //   this.status = {
          //     imgData1: 'http://124.223.72.207:8888/static/Infection/MADDPG/img1.jpg',
          //     imgData2: 'http://124.223.72.207:8888/static/Infection/MADDPG/img2.jpg',
          //     imgData3: 'http://124.223.72.207:8888/static/Infection/MADDPG/img3.jpg',
          //     regionIndex: this.infectionParamMADDPG["Region"],
          //   };
          //   this.drawType = "infection";
          //   this.drawer = true;
          //   return;
          // }
          let formData = new FormData();
          formData.append("filename", this.infectionParamMADDPG["file"][0].raw);
          formData.append("populationFile", this.infectionParamMADDPG["populationFile"][0].raw);
          formData.append("ODFile", this.infectionParamMADDPG["odFile"][0].raw);
          formData.append("R0", this.infectionParamMADDPG["R0"]);
          formData.append("Gamma", this.infectionParamMADDPG["Gamma"]);
          formData.append("Theta", this.infectionParamMADDPG["Theta"]);
          formData.append("Region", this.infectionParamMADDPG["Region"]);
          formData.append("Number", this.infectionParamMADDPG["Number"]);

          let headers = {
            "Content-Type": "application/json",
          };
          const service = axios.create({
            baseURL: serverInfo.baseURL_infection,
            timeout: 16000000
          });
          service
            .post('/MADDPGControl', formData, { headers: headers })
            .then((res) => {
              loading.close();
              this.$message({
                message: "仿真环境上传成功",
                type: "success",
              });
              this.status = {
                imgData1: `data:image/jpg;base64,${res.data.img1}`,
                imgData2: `data:image/jpg;base64,${res.data.img2}`,
                imgData3: `data:image/jpg;base64,${res.data.img3}`,
                regionName: this.infectionParamMADDPG["Region"],
              };
              this.drawType = "infection";
              this.drawer = true;
            })
            .catch((err) => {
              console.log(err);
              loading.close();
              this.$message({
                message: "仿真环境上传失败",
                type: "error",
              });
            });


        }


      }


    },
    getTaskList(callback) {
      ajax("/simulation/processes")
        .then((res) => {
          this.tableData = res.map((r) => {
            let simulationTime;
            if (r.simulationState === 'processing') {
              simulationTime = "计算中";
            }
            else {
              simulationTime = (parseInt(r.simulationTime) / 1000).toString() + " 秒";
            }
            return {
              ...r,
              simulationDate: new Date(r.simulationDate).toLocaleString(),
              simulationTime: simulationTime,
            };
          });
          this.tableData.sort((a, b) => { return a.id - b.id });
          this.$message({
            message: "获取任务列表成功",
            type: "success",
          });
          if(callback){
            callback();
          }
        })
        .catch((err) => {
          console.log(err, 11);
          if (err.message.indexOf("timeout") !== -1) {
            this.$message({
              message: "获取任务列表失败",
              type: "error",
            });
          }
        });
    },
    async clickTask(task) {

      this.drawType = task.simulationType;

      const loading = this.$loading({
        lock: true,
        text: "获取模拟任务数据中",
        spinner: "el-icon-loading",
        background: "rgba(0, 0, 0, 0.7)",
      });
      console.log("SimulationType:", task.simulationType);
      console.log("Task:", task);

      if (task.simulationType === "flood") {

        const resultTypes = ["submergedcells", "availableroads", "dangerousroads"];
        const times = ["4", "8", "12", "16", "20", "24"];
        let resCount = 0, resultLens = {};
        resultTypes.forEach((resultType) => {
          resultLens[resultType] = {};
          times.forEach(async (time) => {
            try {
              let len = await ajax(`information/simulation/length/${task.id}/${resultType}/${time}`);
              resultLens[resultType][`${time}`] = len;
              console.log(`获取模拟数据 ${resultType}:${time} 成功！`);
            }
            catch {
              console.log(`获取模拟数据 ${resultType}:${time} 失败！`);
            }
            finally {
              resCount++;
              //所有请求结束
              if (resCount >= resultTypes.length * times.length) {
                this.status = {
                  resultLens: resultLens,
                  date: task.simulationDate,
                  area: task.simulationArea,
                  id: task.id,
                  computeTime: task.simulationTime,
                };
                this.$message({
                  message: "获取模拟任务数据完成",
                  type: "success",
                });
                loading.close();
                this.drawer = true;
              }
            }
          });
        });


      }
      else {
        this.infectionModel = "gamma";
        const times = ["0", "12", "24", "36", "48", "60", "72"];
        let resCount = 0, resultLens = {};
        times.forEach(async (time) => {
          try {
            let len = await ajax(`/information/simulation/length/${task.id}/infectcells/${time}`);
            resultLens[`${time}`] = len;
            console.log(`获取模拟数据 infectcells:${time} 成功！`);
          }
          catch {
            console.log(`获取模拟数据 infectcells:${time} 失败！`);
          }
          finally {
            resCount++;
            //所有请求结束
            if (resCount >= times.length) {
              this.status = {
                resultLens: {
                  "infectcells": resultLens,
                },
                date: task.simulationDate,
                area: task.simulationArea,
                id: task.id,
                computeTime: task.simulationTime,
              };
              this.$message({
                message: "获取模拟任务数据完成",
                type: "success",
              });
              loading.close();
              this.drawer = true;
            }
          }
        });

      }


    },
    requestMeasure() {
      let currentTask = this.tableData[this.tableData.length - 1];

      let formData = new FormData();
      formData.append("id", currentTask.id);
      formData.append("city", currentTask.simulationArea);

      let url = serverInfo.baseURL + "/measure/resilence";
      let headers = {
        "Content-Type": "multipart/form-data",
      };
      axios
        .post(url, formData, { headers: headers })
        .then((res) => {
          if (res.data.success) {
            this.$message({
              message: res.data.data,
              type: "success",
            });
          }
          else {
            this.$message({
              message: res.data.errorMsg,
              type: "warning",
            });
          }
        })
        .catch((err) => {
          console.log(err);
          this.$message({
            message: "生成交通韧性测度计算任务失败",
            type: "error",
          });
        });
    },
  },
};
</script>

<style lang="scss" scoped>
.helpTitle {
  text-align: center;
  font-weight: bold;
  font-size: large;
  margin: 5px 0;
}

.el-icon-question:hover {
  cursor: pointer;
}
</style>
