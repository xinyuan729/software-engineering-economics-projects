<template>
  <div class="main-wrapper" flex flex-box="1">
    <div flex-box="1" flex="dir:col">
      <div class="border-box" flex-box="1" flex="dir:col">
        <div class="show-board">
          <div>
            <span>
              <span class="color-block" style="background: #00ffff"></span>淹没路网
            </span>
            <span>
              <span class="color-block" style="background: #008000"></span>可用路网
            </span>
            <span>
              <span class="color-block" style="background: #0000ff"></span>淹没区域
            </span>
            <span>
              <span class="color-block" style="background: #ff0000"></span>危险区域
            </span>
            <span>
              <span class="color-block" style="background: #ef9308"></span>危险点
            </span>
          </div>
          <div class="time-step-controler">
            <el-steps align-center :active="active" style="width: 70%">
              <el-step title="时刻:4"></el-step>
              <el-step title="时刻:8"></el-step>
              <el-step title="时刻:12"></el-step>
              <el-step title="时刻:16"></el-step>
              <el-step title="时刻:20"></el-step>
              <el-step title="时刻:24"></el-step>
            </el-steps>
            <el-button style="margin-left: 16px" @click="previous" :disabled="eleDisabled || active < 1" type="primary">
              上一时刻
            </el-button>
            <el-button style="margin-left: 16px" @click="next" :disabled="eleDisabled || active > 4" type="primary">下一时刻
            </el-button>
          </div>
          <div id="canvasName" class="canvasName"> <strong>当前视图：</strong> {{ canvasNames[canvasIndex] }} </div>
          <div id="resultCanvas" class="resultCanvas" v-loading="showLoading" v-show="showResult">
            <div id="panel" class="panel">
              <div class="panel-info">
                <div class="card">
                  <div class="line">
                    <p class="label">
                      <i class="el-icon-date"></i>
                      时间：
                    </p>
                    <p>{{ status.date }}</p>
                  </div>
                  <div class="line">
                    <p class="label">
                      <i class="el-icon-position"></i>
                      位置：
                    </p>
                    <p>{{ parseCity(status.area) }}</p>
                  </div>
                  <div class="line">
                    <p class="label">
                      <i class="el-icon-heavy-rain"></i>
                      淹没区域数量：
                    </p>
                    <p>{{ waterCount }}</p>
                  </div>
                  <div class="line">
                    <p class="label">
                      <i class="el-icon-s-grid"></i>
                      淹没总面积：
                    </p>
                    <p>{{ waterArea.toFixed(6) }} m²</p>
                  </div>
                  <div class="line">
                    <p class="label">
                      <i class="el-icon-time"></i>
                      模拟计算时间：
                    </p>
                    <p>{{ computeTime }}</p>
                  </div>
                  <div class="line">
                    <p class="label">
                      <i class="el-icon-view"></i>
                      当前视图：
                    </p>
                    <p>{{ canvasNames[canvasIndex] }}</p>
                  </div>
                </div>
                <div class="card">
                  <div class="line">
                    <p class="label">
                      重置视图：
                    </p>
                    <button class="controlButton" @click="resetView">
                      <i class="el-icon-location-information controlIcon"></i>
                    </button>
                  </div>
                  <div class="line">
                    <p class="label">
                      淹没区域显示：
                    </p>
                    <el-switch v-model="showSubmergedcells" @change="onDisplayChange" active-color="#5177d2">
                    </el-switch>
                  </div>
                  <div class="line">
                    <p class="label">
                      淹没路网显示：
                    </p>
                    <el-switch v-model="showDangerousroads" @change="onDisplayChange" active-color="#5177d2">
                    </el-switch>
                  </div>
                  <div class="line">
                    <p class="label">
                      可用路网显示：
                    </p>
                    <el-switch v-model="showAvailableroads" @change="onDisplayChange" active-color="#5177d2">
                    </el-switch>
                  </div>
                  <div class="line" v-show="showRisk">
                    <p class="label">
                      危险点显示：
                    </p>
                    <el-switch v-model="showRiskPoints" @change="onDisplayChange" active-color="#5177d2">
                    </el-switch>
                  </div>
                  <div class="line" v-show="showRisk">
                    <p class="label">
                      危险区域显示：
                    </p>
                    <el-switch v-model="showRiskCells" @change="onDisplayChange" active-color="#5177d2">
                    </el-switch>
                  </div>
                </div>
                <div class="card">
                  <div class="line">
                    <p class="label">
                      返回地图：
                    </p>
                    <button class="controlButton" @click="goBackToMap">
                      <i class="el-icon-back controlIcon"></i>
                    </button>
                  </div>
                  <div class="line">
                    <p class="label">
                      显示风险区域和风险点：
                    </p>
                    <button class="controlButton" @click="onDisplayRisk">
                      <i class="el-icon-warning-outline controlIcon"></i>
                    </button>
                  </div>

                </div>
                <div class="card">
                  <div class="line">
                    <p class="label">
                      显示交通韧性：
                    </p>
                    <button class="controlButton" @click="onDisplayChart">
                      <i class="el-icon-pie-chart controlIcon"></i>
                    </button>
                  </div>
                  <div class="line">
                    <p class="label">
                      显示道路连通子图：
                    </p>
                    <button class="controlButton" @click="onDisplaySubgraph" v-show="!subgraphLoading">
                      <i class="el-icon-s-grid controlIcon"></i>
                    </button>
                    <button class="controlButton controlButtonDis" disabled v-show="subgraphLoading">
                      <i class="el-icon-loading"></i>
                    </button>
                  </div>
                  <div class="line">
                    <p class="label">
                      显示医疗服务测度：
                    </p>
                    <button class="controlButton" @click="onDisplayHospital">
                      <i class="el-icon-office-building controlIcon"></i>
                    </button>
                  </div>
                  <div class="line">
                    <p class="label">
                      显示医疗变化测度：
                    </p>
                    <button class="controlButton" @click="onDisplayMedicalChange">
                      <i class="el-icon-office-building controlIcon"></i>
                    </button>
                  </div>
                </div>

                <div class="card">
                  <div class="line">
                    <p class="label">
                      导出结果：
                    </p>
                    <button class="controlButton" @click="showResultDialog">
                      <i class="el-icon-download controlIcon"></i>
                    </button>
                  </div>
                </div>
              </div>
            </div>
            <div id="containerCard" class="containerCard">
              <div :id="'aMap' + idSuffix" class="aMap" v-show="canvasIndex == 0"></div>
              <rainstormSimulationThree v-show="canvasIndex == 1" ref="childThree" />
              <div :id="'chart' + idSuffix" class="chart" v-show="canvasIndex == 2"></div>
              <div :id="'aMapRisk' + idSuffix" class="aMap" v-show="canvasIndex == 3"></div>
              <div :id="'aMapHospital' + idSuffix" class="aMap" v-show="(canvasIndex == 4)"></div>
              <div :id="'aMapSubgraph' + idSuffix" class="aMap" v-show="(canvasIndex == 5)"></div>
              <!-- 弹窗 -->
              <el-popover placement="right" width="300" trigger="manual" ref="mapInfoPopover">
                <div id="popup" slot="reference"></div>
                <div style="text-align: center;">
                  <span style="color:#000;font-weight:bold;">{{ popInfo.id }}号淹没区域</span>
                  <p style="color:#000"><strong>坐标：</strong>{{
                    `(${popInfo.coord[0].toFixed(3)},${popInfo.coord[1].toFixed(3)})` }}</p>
                  <p style="color:#000"><strong>面积：</strong>{{ popInfo.area.toFixed(10) }} 平方米</p>
                  <span>
                    <el-button @click="closePopover">取消</el-button>
                    <el-button type="primary" @click="displayThree">进入</el-button>
                  </span>
                </div>
              </el-popover>
              <el-popover placement="right" width="300" trigger="manual" ref="mapInfoPopoverHostipal">
                <div id="popupHostipal" slot="reference"></div>
                <div style="text-align: center;">
                  <span style="color:#000;font-weight:bold;">{{ popInfoHostipal.id }}号医疗服务测度点</span>
                  <p style="color:#000"><strong>名称：</strong>{{ popInfoHostipal.name }}</p>
                  <p style="color:#000"><strong>地址：</strong>{{ popInfoHostipal.address }}</p>
                  <p style="color:#000"><strong>医疗服务测度值：</strong>{{ popInfoHostipal.value.toFixed(4) }}</p>
                  <span>
                    <el-button @click="closePopoverHostipal">关闭</el-button>
                  </span>
                </div>
              </el-popover>
            </div>
          </div>
        </div>
      </div>
    </div>
    <el-dialog title="导出模拟结果" :visible.sync="dialogVisible" :modal-append-to-body="false" width="600px" :modal="false"
      :center="true">
      <div v-loading="resultPacking">
        <el-table ref="multipleTable" :data="resultFiles" tooltip-effect="dark" style="width: 100%"
          @selection-change="resultSelectionChange">
          <el-table-column type="selection" width="55" align="center">
          </el-table-column>
          <el-table-column prop="type" label="类型" width="120" align="center">
          </el-table-column>
          <el-table-column prop="time" label="时刻" width="120" align="center">
          </el-table-column>
          <el-table-column prop="name" label="文件名" align="center">
          </el-table-column>
          <el-table-column prop="number" label="文件数量" width="100" align="center">
          </el-table-column>
        </el-table>
      </div>
      <span slot="footer" class="dialog-footer">
        <el-button @click="dialogVisible = false">取消</el-button>
        <el-button type="primary" @click="downloadResult">下载</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import axios from "axios";
import serverInfo from '../serverInfo';
import ajax from "../ajax";
import ajaxBlob from "../ajaxBlob";
import ajax2 from "../ajax2";

import rainstormSimulationThree from "./rainstormSimulationThree.vue";

import * as echarts from 'echarts';
import echartTheme from '@/assets/echartTheme.json';

import { Color } from 'three';

import JSZip from 'jszip';
import { saveAs } from 'file-saver';

import mapData from './mapData';
import projzh from './proj';

// ol
import 'ol/ol.css';
import Map from 'ol/Map';
import View from 'ol/View';
import * as olProj from 'ol/proj';
import { Tile as TileLayer, Vector as VectorLayer } from 'ol/layer';
import XYZ from 'ol/source/XYZ';
import { OSM, Vector as VectorSource } from 'ol/source.js';
import WKT from 'ol/format/WKT.js';
import { Fill, Stroke, Style } from 'ol/style.js';
import CircleStyle from "ol/style/Circle";
import Overlay from 'ol/Overlay.js';
import Feature from "ol/Feature";
import { Point } from "ol/geom";

// 坐标系转换
const initOL = (bindThis) => {
  const that = bindThis;
  const gcj02Extent = [-20037508.342789244, -20037508.342789244, 20037508.342789244, 20037508.342789244];
  const gcjMecator = new olProj.Projection({
    code: "GCJ-02",
    extent: gcj02Extent,
    units: "m"
  });
  olProj.addProjection(gcjMecator);
  olProj.addCoordinateTransforms("EPSG:4326", gcjMecator, projzh.ll2gmerc, projzh.gmerc2ll);
  olProj.addCoordinateTransforms("EPSG:3857", gcjMecator, projzh.smerc2gmerc, projzh.gmerc2smerc);
  that.gcjMecator = gcjMecator;
}

// 初始化OpenLayer地图
const initOLMap = (bindThis) => {
  const that = bindThis;
  // 获取地图容器
  let mapDom = document.getElementById('aMap' + that.idSuffix);
  // 初始化地图配置
  that.map = new Map({
    target: mapDom, // 地图容器
    view: new View({
      //设置你的地图初始中心点
      center: mapData[that.status.area].center, // 地图中心点
      zoom: that.zoomLevel, // 缩放
      maxZoom: 17,//最大缩放层级
      minZoom: 10,//最小缩放层级
      extent: mapData[that.status.area].extent, //显示范围
      projection: 'EPSG:4326' // 坐标系
    })
  });
  // 添加一个使用离线瓦片地图的层
  const offlineMapLayer = new TileLayer({
    source: new XYZ({
      projection: that.gcjMecator,
      url: serverInfo.baseURL_three + `/static/mapData/${that.status.area}/{z}/{x}/{y}.png`  // 设置本地离线瓦片所在路径
    })
  });
  // 将图层添加到地图
  that.map.addLayer(offlineMapLayer);

}

// 新建地图，显示初始时刻的模拟结果
const buildMap = async (bindThis) => {
  const that = bindThis;
  try {
    that.showLoading = true;
    that.eleDisabled = true;
    initOLMap(that);


    // 淹没区域
    try {
      const res = await that.getSimulationResult("submergedcells", '4');
      const processResult = processArea(that, res);
      const polygonLayer = processResult.layer;
      polygonLayer.set("layerName", "submergedcells#4");
      if (that.showSubmergedcells) {
        that.map.addLayer(polygonLayer);
        setTimeout(function () {
          that.map.removeLayer(polygonLayer);
          that.map.addLayer(polygonLayer);
        }, 2000);
      }
      that.currentSubmergedcellsLayer = polygonLayer;
      that.waterCount = that.status.resultLens["submergedcells"]['4'];;
      that.waterArea = processResult.area;
    }
    catch {
      that.$message({
        message: `获取第4时刻淹没区域数据失败`,
        type: "error",
      });
    }

    // 淹没路网
    try {
      const res = await that.getSimulationResult("dangerousroads", '4');
      const dangerousroadsResult = processRoads(that, res, '#00ffff');
      if (that.showDangerousroads)
        that.map.addLayer(dangerousroadsResult.layer);
      that.currentDangerousroadsLayer = dangerousroadsResult.layer;
      that.currentDangerousroadsLayer.set("layerName", "dangerousroads#4");
    }
    catch {
      that.$message({
        message: `获取第4时刻淹没路网数据失败`,
        type: "error",
      });
    }

    // 可用路网
    try {
      const res = await that.getSimulationResult("availableroads", '4');
      const availableroadsResult = processRoads(that, res, '#008000');
      if (that.showAvailableroads)
        that.map.addLayer(availableroadsResult.layer);
      that.currentAvailableroadsLayer = availableroadsResult.layer;
      that.currentAvailableroadsLayer.set("layerName", "availableroads#4");
    }
    catch {
      that.$message({
        message: `获取第4时刻可用路网数据失败`,
        type: "error",
      });
    }

    that.showLoading = false;
    that.eleDisabled = false;
    that.showResult = true;

  }
  catch (e) {
    console.error("BuildMap:", e);
    that.$message.error(`发生了错误:${JSON.stringify(e)}`);
  }
}

// 获取并显示对应时刻的模拟数据
const buildMapAtTime = async (bindThis, time) => {
  const that = bindThis;
  try {
    that.showLoading = true;
    that.eleDisabled = true;

    // 淹没区域
    try {
      const res = await that.getSimulationResult("submergedcells", `${time}`);
      const processResult = processArea(that, res);
      const polygonLayer = processResult.layer;
      polygonLayer.set("layerName", "submergedcells#" + `${time}`);
      that.map.removeLayer(that.currentSubmergedcellsLayer);
      if (that.showSubmergedcells)
        that.map.addLayer(polygonLayer);
      that.currentSubmergedcellsLayer = polygonLayer;
      that.waterCount = that.status.resultLens["submergedcells"][`${time}`];
      that.waterArea = processResult.area;
    }
    catch {
      that.$message({
        message: `获取第${time}时刻淹没区域数据失败`,
        type: "error",
      });
    }

    // 淹没路网
    try {
      const res = await that.getSimulationResult("dangerousroads", `${time}`);
      const dangerousroadsResult = processRoads(that, res, '#00ffff');
      that.map.removeLayer(that.currentDangerousroadsLayer);
      if (that.showDangerousroads)
        that.map.addLayer(dangerousroadsResult.layer);
      that.currentDangerousroadsLayer = dangerousroadsResult.layer;
      that.currentDangerousroadsLayer.set("layerName", "dangerousroads#" + `${time}`);
    }
    catch {
      that.$message({
        message: `获取第${time}时刻淹没路网数据失败`,
        type: "error",
      });
    }

    // 可用路网
    try {
      const res = await that.getSimulationResult("availableroads", `${time}`);
      const availableroadsResult = processRoads(that, res, '#008000');
      that.map.removeLayer(that.currentAvailableroadsLayer);
      if (that.showAvailableroads)
        that.map.addLayer(availableroadsResult.layer);
      that.currentAvailableroadsLayer = availableroadsResult.layer;
      that.currentAvailableroadsLayer.set("layerName", "availableroads#" + `${time}`);
    }
    catch {
      that.$message({
        message: `获取第${time}时刻可用路网数据失败`,
        type: "error",
      });
    }


    that.showLoading = false;
    that.eleDisabled = false;
  } catch (e) {
    that.$message.error(`获取模拟数据失败:${JSON.stringify(e)}`);
    console.error(e);
  }
};


// 新建查看风险地图
const buildMapRisk = async (bindThis) => {
  const that = bindThis;
  try {

    // 获取地图容器
    let mapDom = document.getElementById('aMapRisk' + that.idSuffix);
    // 初始化地图配置
    that.mapRisk = new Map({
      target: mapDom, // 地图容器
      view: new View({
        //设置你的地图初始中心点
        center: mapData[that.status.area].center, // 地图中心点
        zoom: that.zoomLevel, // 缩放
        maxZoom: 17,//最大缩放层级
        minZoom: 10,//最小缩放层级
        extent: mapData[that.status.area].extent, //显示范围
        projection: 'EPSG:4326' // 坐标系
      })
    });
    // 添加一个使用离线瓦片地图的层
    const offlineMapLayer = new TileLayer({
      source: new XYZ({
        projection: that.gcjMecator,
        url: serverInfo.baseURL_three + `/static/mapData/${that.status.area}/{z}/{x}/{y}.png`  // 设置本地离线瓦片所在路径
      })
    });
    // 将图层添加到地图
    that.mapRisk.addLayer(offlineMapLayer);

  } catch (e) {
    console.error(e, 888);
    that.$message.error(`查看风险地图失败:${JSON.stringify(e)}`);
  }

};

// 新建查看医疗测度地图
const buildMapHospital = async (bindThis) => {
  const that = bindThis;
  try {

    // 获取地图容器
    let mapDom = document.getElementById('aMapHospital' + that.idSuffix);
    // 初始化地图配置
    that.mapHospital = new Map({
      target: mapDom, // 地图容器
      view: new View({
        //设置你的地图初始中心点
        center: mapData[that.status.area].center, // 地图中心点
        zoom: that.zoomLevel, // 缩放
        maxZoom: 17,//最大缩放层级
        minZoom: 10,//最小缩放层级
        extent: mapData[that.status.area].extent, //显示范围
        projection: 'EPSG:4326' // 坐标系
      })
    });
    // 添加一个使用离线瓦片地图的层
    const offlineMapLayer = new TileLayer({
      source: new XYZ({
        projection: that.gcjMecator,
        url: serverInfo.baseURL_three + `/static/mapData/${that.status.area}/{z}/{x}/{y}.png`  // 设置本地离线瓦片所在路径
      })
    });
    // 将图层添加到地图
    that.mapHospital.addLayer(offlineMapLayer);

  } catch (e) {
    console.error(e, 888);
    that.$message.error(`发生了错误:${JSON.stringify(e)}`);
  }

};

// 新建查看道路连通子图地图
const buildMapSubgraph = async (bindThis) => {
  const that = bindThis;
  try {

    // 获取地图容器
    let mapDom = document.getElementById('aMapSubgraph' + that.idSuffix);
    // 初始化地图配置
    that.mapSubgraph = new Map({
      target: mapDom, // 地图容器
      view: new View({
        //设置你的地图初始中心点
        center: mapData[that.status.area].center, // 地图中心点
        zoom: that.zoomLevel, // 缩放
        maxZoom: 17,//最大缩放层级
        minZoom: 10,//最小缩放层级
        extent: mapData[that.status.area].extent, //显示范围
        projection: 'EPSG:4326' // 坐标系
      })
    });
    // 添加一个使用离线瓦片地图的层
    const offlineMapLayer = new TileLayer({
      source: new XYZ({
        projection: that.gcjMecator,
        url: serverInfo.baseURL_three + `/static/mapData/${that.status.area}/{z}/{x}/{y}.png`  // 设置本地离线瓦片所在路径
      })
    });
    // 将图层添加到地图
    that.mapSubgraph.addLayer(offlineMapLayer);

  } catch (e) {
    console.error(e, 888);
    that.$message.error(`发生了错误:${JSON.stringify(e)}`);
  }

};


// --- 处理淹没区域数据，转化为多边形覆盖物 ---
function processArea(bindThis, data) {
  const that = bindThis;
  let totalArea = 0;
  let polygonFeatures = [];
  data.forEach((item, index) => {
    const format = new WKT();
    const feature = format.readFeature(item.geom, {
      dataProjection: 'EPSG:4326',
      featureProjection: 'EPSG:4326',
    });
    feature.name = "area";
    polygonFeatures.push(feature);
    let geometry = feature.getGeometry();
    let area = geometry.getArea() * 1000000;
    let coord = geometry.getFirstCoordinate();
    totalArea += area;
    feature.info = {
      id: index,
      coord: coord,
      area: area,
    };

  });
  const style = new Style({
    stroke: new Stroke({
      color: 'rgba(0, 0, 255, 0.6)',
      width: 0,
    }),
    fill: new Fill({
      color: 'rgba(0, 0, 255, 0.75)',
    }),
  });
  const selectStyle = new Style({
    stroke: new Stroke({
      color: '#7beeee',
      width: 0,
    }),
    fill: new Fill({
      color: '#7beeee',
    }),
  });

  const layer = new VectorLayer({
    source: new VectorSource({
      features: polygonFeatures,
    }),
    style: style,
  });

  let selected = null;
  // 鼠标移动事件
  that.map.on('pointermove', function (event) {
    if (selected !== null) {
      selected.setStyle(style);
      selected = null;
    }
    that.map.forEachFeatureAtPixel(event.pixel, function (f) {
      // 移动到多边形上改变颜色
      if (f.name === "area") {
        selected = f;
        f.setStyle(selectStyle);
      }
      return true;
    });
    if (selected) {
      that.map.getTargetElement().style.cursor = "pointer";
    }
    else {
      that.map.getTargetElement().style.cursor = "auto";
    }
  });
  // 鼠标点击事件
  const popup = new Overlay({
    element: document.getElementById('popup'),
  });
  that.map.addOverlay(popup);

  that.map.on('click', function (event) {
    let click = null;
    that.map.forEachFeatureAtPixel(event.pixel, function (f) {
      if (f.name === "area") {
        const coordinate = event.coordinate;
        that.$refs.mapInfoPopover.showPopper = false;
        that.popInfo = f.info;
        click = f;
        setTimeout(() => {
          popup.setPosition(coordinate);
          that.$refs.mapInfoPopover.showPopper = true;
        }, 100);
      }
      return true;
    });
    if (!click) {
      that.$refs.mapInfoPopover.showPopper = false;
    }
  });

  return {
    layer: layer,
    count: polygonFeatures.length,
    area: totalArea,
  };

}

// --- 处理路网数据，转化为折线覆盖物 ---
function processRoads(bindThis, data, color) {
  const that = bindThis;
  let lineFeatures = [];

  data.forEach((item, index) => {
    const format = new WKT();
    const feature = format.readFeature(item.geom, {
      dataProjection: 'EPSG:4326',
      featureProjection: 'EPSG:4326',
    });
    lineFeatures.push(feature);

  });
  const style = new Style({
    stroke: new Stroke({
      color: color,
      width: 6,
    }),
  });
  const layer = new VectorLayer({
    source: new VectorSource({
      features: lineFeatures,
    }),
    style: style,
  });
  return {
    layer: layer,
    count: lineFeatures.length,
  }

}

// --- 处理风险点数据，转化为点覆盖物 ---
function processRiskPoints(bindThis, data) {
  const that = bindThis;
  let pointFeatures = [];
  data.forEach((item, index) => {
    const format = new WKT();
    const feature = format.readFeature(item.geom, {
      dataProjection: 'EPSG:4326',
      featureProjection: 'EPSG:4326',
    });
    pointFeatures.push(feature);

  });
  const style = new Style({
    image: new CircleStyle({
      fill: new Fill({
        color: '#ef9308',
      }),
      radius: 5,
    })
  });
  const layer = new VectorLayer({
    source: new VectorSource({
      features: pointFeatures,
    }),
    style: style,
  });
  return {
    layer: layer,
    count: pointFeatures.length
  };
}

// --- 处理风险区域数据，转化为多边形覆盖物 ---
function processRiskCells(bindThis, data) {
  const that = bindThis;
  let totalArea = 0;
  let polygonFeatures = [];
  data.forEach((item, index) => {
    const format = new WKT();
    const feature = format.readFeature(item.geom, {
      dataProjection: 'EPSG:4326',
      featureProjection: 'EPSG:4326',
    });
    polygonFeatures.push(feature);
    let geometry = feature.getGeometry();
    let area = geometry.getArea() * 1000000;
    totalArea += area;


  });
  const style = new Style({
    stroke: new Stroke({
      color: 'rgba(255, 0, 0, 0.6)',
      width: 0,
    }),
    fill: new Fill({
      color: 'rgba(255, 0, 0, 0.8)',
    }),
  });
  const layer = new VectorLayer({
    source: new VectorSource({
      features: polygonFeatures,
    }),
    style: style,
  });

  return {
    layer: layer,
    count: polygonFeatures.length,
    area: totalArea
  };

}

// --- 处理医疗服务数据，转化为点覆盖物 ---
function processHospitalPoints(bindThis, data) {
  const that = bindThis;
  let points = [];
  let pointFeatures = [];

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

    const feature = new Feature({
      geometry: new Point([item.longtitude, item.latitude])
    });
    feature.name = "point";
    feature.setStyle(
      new Style({
        image: new CircleStyle({
          fill: new Fill({
            color: '#' + color.getHexString(),
          }),
          radius: 7,
        })
      })
    )
    feature.info = {
      id: index,
      name: item.name,
      address: item.address,
      value: item.result
    };
    pointFeatures.push(feature);
    return;

  });

  let selected = null;
  let style = null;
  let selectStyle = new Style({
    image: new CircleStyle({
      fill: new Fill({
        color: '#ff8800',
      }),
      radius: 7,
    })
  });
  // 鼠标移动事件
  that.mapHospital.on('pointermove', function (event) {
    if (selected !== null) {
      selected.setStyle(style);
      selected = null;
    }
    that.mapHospital.forEachFeatureAtPixel(event.pixel, function (f) {
      // 移动到多边形上改变颜色
      if (f.name === "point") {
        selected = f;
        style = f.getStyle();
        f.setStyle(selectStyle);
      }
      return true;
    });
    if (selected) {
      that.mapHospital.getTargetElement().style.cursor = "pointer";
    }
    else {
      that.mapHospital.getTargetElement().style.cursor = "auto";
    }
  });
  // 鼠标点击事件
  const popup = new Overlay({
    element: document.getElementById('popupHostipal'),
  });
  that.mapHospital.addOverlay(popup);

  that.mapHospital.on('click', function (event) {
    let click = null;
    that.mapHospital.forEachFeatureAtPixel(event.pixel, function (f) {
      if (f.name === "point") {
        const coordinate = event.coordinate;
        that.$refs.mapInfoPopoverHostipal.showPopper = false;
        that.popInfoHostipal = f.info;
        click = f;
        setTimeout(() => {
          popup.setPosition(coordinate);
          that.$refs.mapInfoPopoverHostipal.showPopper = true;
        }, 100);
      }
      return true;
    });
    if (!click) {
      that.$refs.mapInfoPopoverHostipal.showPopper = false;
    }
  });

  const layer = new VectorLayer({
    source: new VectorSource({
      features: pointFeatures,
    }),
  });
  return {
    layer: layer,
    count: pointFeatures.length
  };

}

// --- 处理道路连通子图数据，转化为折线覆盖物 ---
function processSubgraph(bindThis, data, color) {
  const that = bindThis;
  let lineFeatures = [];
  let maxLen = 300;
  data.forEach((items) => {
    for (let i = 0; i < items.length && i < maxLen; ++i) {
      const format = new WKT();
      const feature = format.readFeature(items[i], {
        dataProjection: 'EPSG:4326',
        featureProjection: 'EPSG:4326',
      });
      lineFeatures.push(feature);
    }

  })
  const style = new Style({
    stroke: new Stroke({
      color: color,
      width: 6,
    }),
  });
  const layer = new VectorLayer({
    source: new VectorSource({
      features: lineFeatures,
    }),
    style: style,
  });
  return {
    layer: layer,
    count: lineFeatures.length,
  }

}

// 绘制交通韧性折线图
const buildChart = async (bindThis) => {
  const that = bindThis;

  echarts.registerTheme('customed', echartTheme);
  that.myChart = echarts.init(document.getElementById('chart' + that.idSuffix), 'customed');
  var option = {
    xAxis: {
      type: 'category',
      data: ['4时', '8时', '12时', '16时', '20时', '24时'],
      name: '时刻',
      nameLocation: 'center',
      nameTextStyle: {
        color: 'black',
        fontSize: 16,
      },
      nameGap: 40,
    },
    yAxis: {
      type: 'value',
      name: '交通韧性值',
      nameLocation: 'center',
      nameTextStyle: {
        color: 'black',
        fontSize: 16,
      },
      nameGap: 40,
    },
    series: [
      {
        data: that.resilenceData,
        type: 'line'
      }
    ]
  };
  // 绘制图表
  that.myChart.setOption(option);

};


export default {
  name: "components_1_4",
  data() {
    return {
      // 一些状态变量
      showLoading: false,
      active: 0,
      eleDisabled: true,
      metricDisabled: true,
      dialogVisible: false,
      isShowRisked: false,

      showResult: true,
      canvasIndex: 0,

      subgraphLoading: false,

      // 地图
      gcjMecator: null,
      map: null,
      mapRisk: null,
      mapHospital: null,
      mapSubgraph: null,

      // 模拟结果数据图层
      currentSubmergedcellsLayer: null,
      currentDangerousroadsLayer: null,
      currentAvailableroadsLayer: null,
      riskPointsLayer: null,
      riskCellsLayer: null,

      currentSubgraphLayer: null,

      // 数据显示状态变量
      showSubmergedcells: true,
      showDangerousroads: false,
      showAvailableroads: false,
      showRisk: false,
      showRiskCells: true,
      showRiskPoints: true,
      showSatellite: false,

      // 当前任务信息
      waterCount: 0,
      waterArea: 0,
      computeTime: 0,

      // 图层
      satelliteLayer: null,
      myChart: null,

      riskCreated: false,

      resilenceData: [],

      canvasNames: {
        0: "模拟结果(每个时刻)",
        1: "人群疏散",
        2: "交通韧性",
        3: "风险区域和风险点(24小时内)",
        4: "医疗服务测度",
        5: "道路连通子图",
      },

      currentSimulationResult: null,
      cityName: "广州",
      zoomLevel: 10,
      resultMaxLen: 10000,
      subgraphMaxLen: 100,

      resultFiles: [],
      resultSelection: [],
      resultPacking: false,
      // 弹窗信息
      popInfo: {
        id: 0,
        coord: [0, 0],
        area: 0,
      },
      popInfoHostipal: {
        id: 0,
        name: "",
        address: "",
        value: 0,
      },
    };
  },
  components: {
    rainstormSimulationThree,
  },
  props: {
    type: {
      type: String,
    },
    status: {
      type: Object,
    },
    metric: {
      type: Object,
    },
    idSuffix: {
      type: String,
      default: "",
    },
  },
  watch: {
    type(newVal) {
      if (newVal) {
        this.active = 0;
        this.showLoading = false;
      }
    },
    status() {
      if (this.type == "flood") {
        this.onStatusChange();
      }
    },
    metric(newVal) {
      this.metricDisabled = false;
    },
  },
  mounted() {
    window.displayThree = this.displayThree;
    window.onresize = () => {
      this.myChart.resize();
    };
    if (this.type == "flood") {
      this.onStatusChange();
    }
  },
  methods: {
    // 传入的status数据改变，即切换显示的任务
    onStatusChange() {
      this.eleDisabled = true;
      this.metricDisabled = true;
      this.dialogVisible = false;
      this.isShowRisked = false;

      this.showSubmergedcells = true;
      this.showDangerousroads = false;
      this.showAvailableroads = false;

      this.showRisk = true;
      this.showRiskCells = true;
      this.showRiskPoints = false;

      this.showSatellite = false;

      this.active = 0;
      this.canvasIndex = 0;

      this.riskCreated = false;

      this.subgraphLoading = false;

      this.computeTime = this.status.computeTime;
      this.currentSimulationResult = {
        "submergedcells": {},
        "availableroads": {},
        "dangerousroads": {},
        "riskcells": {},
        "riskpoints": {},
      };

      if (this.map) {
        this.map.setTarget(null);
        this.map = null;
      }
      if (this.mapRisk) {
        this.mapRisk.setTarget(null);
        this.mapRisk = null;
      }

      this.cityName = this.parseCity(this.status.area);

      initOL(this);
      buildMap(this);

      this.resultFiles = [];
      this.resultSelection = [];
      this.resultPacking = false;
    },
    // 显示风险区域和风险点
    async onShowRisks() {
      try {
        let riskCellsLen = await ajax(`information/simulation/length/${this.status.id}/riskcells/4`);
        const res = await this.getRiskResult("riskcells", '4', riskCellsLen);
        const riskcellsResult = processRiskCells(this, res);
        if (this.showRiskCells)
          this.mapRisk.addLayer(riskcellsResult.layer);
        this.riskCellsLayer = riskcellsResult.layer;
        this.riskCellsLayer.set("layerName", "riskcells");
      }
      catch {
        this.$message({
          message: "获取模拟风险区域数据失败" + (err.message || ""),
          type: "error",
        });
      }

      try {
        let riskPointsLen = await ajax(`information/simulation/length/${this.status.id}/riskpoints/4`);
        const res = await this.getRiskResult("riskpoints", '4', riskPointsLen);
        const riskPointsResult = processRiskPoints(this, res);
        if (this.showRiskPoints)
          this.mapRisk.addLayer(riskPointsResult.layer);
        this.riskPointsLayer = riskPointsResult.layer;
        this.riskPointsLayer.set("layerName", "riskpoints");
      }
      catch {
        this.$message({
          message: "获取模拟风险点数据失败" + (err.message || ""),
          type: "error",
        });
      }

      this.showRisk = true;
    },
    // 上一时刻
    previous() {
      const stepTimeMap = {
        0: 4,
        1: 8,
        2: 12,
        3: 16,
        4: 20,
        5: 24,
      };
      this.active--;
      if (this.active < 0) {
        this.active = 0;
        return;
      }

      buildMapAtTime(this, stepTimeMap[this.active]);
      if (this.canvasIndex == 5) //道路连通子图
      {
        this.displaySubgraphAtTime(stepTimeMap[this.active]);
      }
    },
    // 下一时刻
    next() {
      const stepTimeMap = {
        0: 4,
        1: 8,
        2: 12,
        3: 16,
        4: 20,
        5: 24,
      };
      this.active++;
      if (this.active > 5) {
        this.active = 5;
        return;
      }

      buildMapAtTime(this, stepTimeMap[this.active]);
      if (this.canvasIndex == 5) //道路连通子图
      {
        this.displaySubgraphAtTime(stepTimeMap[this.active]);
      }
    },
    // 重置视图
    resetView() {
      if (this.canvasIndex == 0) {
        this.map.getView().setCenter(mapData[this.status.area].center);
        this.map.getView().setZoom(this.zoomLevel);

      }
      else if (this.canvasIndex == 3) {

        this.mapRisk.getView().setCenter(mapData[this.status.area].center);
        this.mapRisk.getView().setZoom(this.zoomLevel);
      }

    },
    // 为地图添加图层
    onMapAddLayer(layer) {
      const layers = this.map.getLayers();
      const layerName = layer.get("layerName");
      let hasLayer = false;
      layers.forEach((item) => {
        if (item.get("layerName") === layerName)
          hasLayer = true;
      });
      if (!hasLayer)
        this.map.addLayer(layer);
    },
    // 为风险地图添加图层
    onMapRiskAddLayer(layer) {
      const layers = this.mapRisk.getLayers();
      const layerName = layer.get("layerName");
      let hasLayer = false;
      layers.forEach((item) => {
        if (item.get("layerName") === layerName)
          hasLayer = true;
      });
      if (!hasLayer)
        this.mapRisk.addLayer(layer);
    },
    // 切换显示的数据
    onDisplayChange() {
      if (this.showSubmergedcells) {
        this.onMapAddLayer(this.currentSubmergedcellsLayer);
      } else {
        this.map.removeLayer(this.currentSubmergedcellsLayer);
      }

      if (this.showDangerousroads) {
        this.onMapAddLayer(this.currentDangerousroadsLayer);
      } else {
        this.map.removeLayer(this.currentDangerousroadsLayer);
      }

      if (this.showAvailableroads) {
        this.onMapAddLayer(this.currentAvailableroadsLayer);
      } else {
        this.map.removeLayer(this.currentAvailableroadsLayer);
      }
      if (this.showRisk && this.mapRisk) {
        if (this.showRiskPoints) {
          this.onMapRiskAddLayer(this.riskPointsLayer);
        }
        else {
          this.mapRisk.removeLayer(this.riskPointsLayer);
        }
        if (this.showRiskCells) {
          this.onMapRiskAddLayer(this.riskCellsLayer);
        }
        else {
          this.mapRisk.removeLayer(this.riskCellsLayer);
        }
      }

    },
    // 切换到三维场景
    displayThree() {
      this.showThree = true;
      this.canvasIndex = 1;
      this.$refs.childThree.start();
      this.closePopover();
    },
    // 切换到原始二维地图
    goBackToMap() {
      this.showThree = false;
      this.canvasIndex = 0;
      this.$refs.childThree.stop();
      if (this.mapRisk) {
        this.mapRisk.setTarget(null);
        this.mapRisk = null;
      }
      if (this.mapHospital) {
        this.mapHospital.setTarget(null);
        this.mapHospital = null;
      }
      if (this.mapSubgraph) {
        this.mapSubgraph.setTarget(null);
        this.mapSubgraph = null;
      }
    },
    // 解析交通韧性测度数据
    parseMeasureData(data) {
      let items = data.slice(1, -1).split(',');
      let obj = {};
      items.forEach((item) => {
        let item_split = item.split('=');
        let key = Number(item_split[0]);
        let value = Number(item_split[1]);
        obj[key] = value;
      });
      return obj;
    },
    // 显示交通韧性测度曲线
    onDisplayChart() {
      let formData = new FormData();
      formData.append("id", this.status.id);
      formData.append("city", this.cityName);

      let url = serverInfo.baseURL_measure + "/measure/resilence";
      let headers = {
        "Content-Type": "multipart/form-data",
      };
      axios
        .post(url, formData, { headers: headers })
        .then((res) => {
          console.log(res);
          if (res.data.success) {
            if (res.data.data == "生成韧性测度计算任务成功") {
              this.$message({
                message: res.data.data,
                type: "success",
              });
            }
            else {
              let obj = this.parseMeasureData(res.data.data);
              this.resilenceData = Object.values(obj);
              buildChart(this);
              this.myChart.resize({
                width: document.getElementById("containerCard").offsetWidth
              });
              this.canvasIndex = 2;
            }

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
            message: "获取交通韧性测度失败",
            type: "error",
          });
        });


    },
    // 切换到风险区域和风险点视图
    onDisplayRisk() {
      this.canvasIndex = 3;
      buildMapRisk(this);
      setTimeout(() => {
        this.onShowRisks();
      }, 1000);

    },
    // 显示医疗测度服务
    onDisplayHospital() {
      buildMapHospital(this);
      let url = serverInfo.baseURL_measure + "/measure/measureResult?city=" + this.status.area;
      axios
        .post(url)
        .then((res) => {
          console.log(res);
          if (res.data.success) {
            const hospitalResult = processHospitalPoints(this, res.data.data);
            this.mapHospital.addLayer(hospitalResult.layer);
            this.canvasIndex = 4;

          }
          else {
            this.$message({
              message: res.data.errorMsg,
              type: "error",
            });
          }
        })
        .catch((err) => {
          console.log(err);
          this.$message({
            message: "获取医疗服务测度失败",
            type: "error",
          });
        });

    },
    // 显示医疗变化测度
    onDisplayMedicalChange() {
      let url = serverInfo.baseURL_measure + "/measure/medicalServiceChange?" +
        `city=${this.status.area}&id=${this.status.id}`;
      axios
        .post(url)
        .then((res) => {
          console.log(res);
          if (res.data.success) {
            if (res.data.data.status === "RUNNING") {
              this.$message({
                message: "生成医疗变化测度任务成功",
                type: "success",
              });
            }
            else{
              this.$message({
                message: "获取医疗变化测度数据成功",
                type: "success",
              });
              console.log("medicalServiceChangeData:", res.data.data);
            }


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
            message: "获取医疗变化测度失败",
            type: "error",
          });
        });
    },
    // 显示道路连通子图
    async onDisplaySubgraph() {
      buildMapSubgraph(this);
      let url = serverInfo.baseURL_measure + "/measure/subgraphsublist?" +
        `city=${this.status.area}&id=${this.status.id}&time=${4}&start=${0}&end=${this.subgraphMaxLen}`;
      this.$message({
        message: "获取第4时刻道路连通子图数据中",
        type: "warning",
      });
      this.subgraphLoading = true;
      axios
        .post(url)
        .then((res) => {
          console.log(res);
          if (res.data.success) {
            this.$message({
              message: "获取第4时刻道路连通子图数据成功",
              type: "success",
            });
            const result = processSubgraph(this, res.data.data, "#0000ff");
            this.mapSubgraph.addLayer(result.layer);
            this.currentSubgraphLayer = result.layer;
            this.currentSubgraphLayer.set("layerName", "subgraph#0");


            this.canvasIndex = 5;
            this.subgraphLoading = false;
          }
          else {
            this.$message({
              message: "获取第4时刻道路连通子图数据失败：" + res.data.errorMsg,
              type: "error",
            });
          }
        })
        .catch((err) => {
          console.log(err);
          this.$message({
            message: "获取第4时刻道路连通子图数据失败",
            type: "error",
          });
        });
    },
    // 显示对应时刻道路连通子图
    async displaySubgraphAtTime(time) {

      let url = serverInfo.baseURL_measure + "/measure/subgraphsublist?" +
        `city=${this.status.area}&id=${this.status.id}&time=${time}&start=${0}&end=${this.subgraphMaxLen}`;
      this.$message({
        message: `获取第${time}时刻道路连通子图数据中`,
        type: "warning",
      });
      this.subgraphLoading = true;

      axios
        .post(url)
        .then((res) => {
          console.log(res);
          if (res.data.success) {
            this.$message({
              message: `获取第${time}时刻道路连通子图数据成功`,
              type: "success",
            });
            this.mapSubgraph.removeLayer(this.currentSubgraphLayer);
            this.currentSubgraphLayer = null;
            const result = processSubgraph(this, res.data.data, "#0000ff");
            this.mapSubgraph.addLayer(result.layer);
            this.currentSubgraphLayer = result.layer;
            this.currentSubgraphLayer.set("layerName", "subgraph#" + `${time}`);

            this.subgraphLoading = false;
          }
          else {
            this.$message({
              message: `获取第${time}时刻道路连通子图数据失败：` + res.data.errorMsg,
              type: "error",
            });
          }
        })
        .catch((err) => {
          console.log(err);
          this.$message({
            message: `获取第${time}时刻道路连通子图数据失败`,
            type: "error",
          });
        });

    },
    // 城市拼音转中文
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
    // 获取模拟结果
    async getSimulationResult(resultType, time) {
      let res;
      if (time in this.currentSimulationResult[resultType]) {
        res = this.currentSimulationResult[resultType][time];
      }
      else {
        let len = this.status.resultLens[resultType][time];
        if (len > this.resultMaxLen) len = this.resultMaxLen;
        res = await ajax(`information/simulation/subresult/${this.status.id}/${resultType}/${time}/0/${len}`);
        this.currentSimulationResult[resultType][time] = res;
      }
      return res;
    },
    // 获取风险点或风险区域
    async getRiskResult(resultType, time, reslLen) {
      let res;
      if (time in this.currentSimulationResult[resultType]) {
        res = this.currentSimulationResult[resultType][time];
      }
      else {
        let len = reslLen;
        if (len > this.resultMaxLen) len = this.resultMaxLen;
        res = await ajax(`information/simulation/subresult/${this.status.id}/${resultType}/${time}/0/${len}`);
        this.currentSimulationResult[resultType][time] = res;
      }
      return res;
    },
    // 勾选结果文件
    resultSelectionChange(val) {
      this.resultSelection = val;
    },
    // 获取模拟结果文件列表
    async getResultFileList() {
      let getURL = (fileName) => {
        return `information/simulation/resultDownload/flood/${this.status.id}/${this.status.area}?path=${fileName}`;
      };

      const resultTypes = ["submergedcells", "availableroads", "dangerousroads"];
      const resultPaths = {
        "submergedcells": "submerged_cells",
        "availableroads": "roads/available_roads",
        "dangerousroads": "roads/dangerous_roads",
      };
      const fileNames = {
        "submergedcells": "submerged_cells",
        "availableroads": "available_roads",
        "dangerousroads": "dangerous_roads",
      };
      const resultNames = {
        "submergedcells": "淹没区域",
        "availableroads": "可用路网",
        "dangerousroads": "淹没路网",
      };
      const times = ["4", "8", "12", "16", "20", "24"];
      const fileTypes = ["shp", "shx", "pdj", "fix", "dbf"];

      resultTypes.forEach((resultType) => {
        times.forEach((time) => {
          if (time in this.status.resultLens[resultType]) {
            let resultItems = [];
            fileTypes.forEach((fileType) => {
              resultItems.push({
                name: `${fileNames[resultType]}${time}.${fileType}`,
                url: getURL(`${resultPaths[resultType]}${time}.${fileType}`),
              });
            });
            this.resultFiles.push({
              type: `${resultNames[resultType]}`,
              time: `${time}时`,
              name: `${fileNames[resultType]}${time}.*`,
              files: resultItems,
              number: resultItems.length,
            });
          }
        });
      });

      try {
        let riskCellsLen = await ajax(`information/simulation/length/${this.status.id}/riskcells/4`);
        if (riskCellsLen) {
          let resultItems = [];
          fileTypes.forEach((fileType) => {
            resultItems.push({
              name: `risk_cells.${fileType}`,
              url: getURL(`risk_cells.${fileType}`),
            });
          });
          this.resultFiles.push({
            type: "风险区域",
            time: "4-24时",
            name: `risk_cells.*`,
            files: resultItems,
            number: resultItems.length,
          });
        }
      }
      catch { }
      this.resultFiles.push({
        type: "性能报告",
        time: "-",
        name: `performance.txt`,
        files: [{
          name: "performance.txt",
          url: getURL("performance.txt"),
        }],
        number: 1,
      });

    },
    // 显示导出模拟结果对话框
    async showResultDialog() {
      this.$message({
        message: "开始查询模拟结果文件",
        type: "success",
      });
      this.showLoading = true;
      await this.getResultFileList();
      this.$message({
        message: "查询模拟结果文件成功",
        type: "success",
      });
      this.showLoading = false;
      this.dialogVisible = true;
    },
    // 导出模拟结果
    async downloadResult() {
      console.log("resultSelection:", this.resultSelection);
      this.$message({
        message: "开始打包模拟结果文件",
        type: "success",
      });
      this.resultPacking = true;
      let zipFile = new JSZip();
      for (let i = 0; i < this.resultSelection.length; ++i) {
        let files = this.resultSelection[i].files;
        for (let j = 0; j < files.length; ++j) {
          try {
            let file = files[j];
            let data = await ajaxBlob(file.url);
            if (file.name === "performance.txt")
              zipFile.file(file.name, data, { binary: false });
            else
              zipFile.file(file.name, data, { binary: true });
          }
          catch {
            console.log(`下载文件 ${file.url} 失败`);
          }
        }
      }

      zipFile.generateAsync({ type: "blob" })
        .then((blob) => {
          saveAs(blob, `results_${this.status.area}_flood_${this.status.id}`);
          this.resultPacking = false;
        }, (e) => {
          this.$message({
            message: e,
            type: "error",
          });
          this.resultPacking = false;
        });
    },
    // 关闭地图内弹窗
    closePopover() {
      this.$refs.mapInfoPopover.showPopper = false;
    },
    closePopoverHostipal() {
      this.$refs.mapInfoPopoverHostipal.showPopper = false;
    },
  },
};
</script>

<style>
.el-step__title.is-process {
  color: #fff !important;
}
</style>
<style lang="scss" scoped>
.color-block {
  display: inline-block;
  width: 10px;
  height: 10px;
  margin: 0 4px 0 8px;
}

.map-container {
  width: 100%;
  height: 100%;
  padding: 24px;
}

.wsmMap {
  width: 100%;
  height: calc(100% - 96px);
}

.time-step-controler {
  display: flex;
  align-items: center;
  height: 60px;
  margin: 8px 0;
}

.border-box {
  padding-left: 24px;
  padding-bottom: 0;
  padding-top: 0;
}

.show-board {
  padding-top: 50px;
  padding-bottom: 12px;
  height: 100%;
}


.resultCanvas {
  height: calc(100% - 96px);
  height: 100%;
  display: flex;
  flex-direction: row;
  overflow: hidden;
}

.canvasName {
  padding-left: 260px;
  ;
}

.panel {
  width: 250px;
  height: 100%;
  padding-left: 5px;
  display: flex;
  flex-direction: column;

  .line {
    display: flex;
    flex-direction: row;
    align-items: center;
  }

  .label {
    font-weight: bold;
  }

  p {
    font-size: 16px;
    color: #333;
    margin: 5px 0;
  }


  .panel-info {
    border-radius: 10px;
    transition: 0.3s;
    flex-grow: 1;

  }

  .card {
    border-radius: 10px;
    padding: 15px;
    margin: 10px 0;
    box-shadow: 5px 4px 4px 1px rgba(39, 56, 98, 0.2);
    /* background: rgb(102,197,222);
  background: linear-gradient(135deg, rgba(102,197,222,1) 0%, rgba(81,119,210,1) 100%); */
    background-color: #fff;
    transition: 0.3s;
  }

  .card:hover {
    box-shadow: 5px 4px 4px 3px rgba(39, 56, 98, 0.5);
  }

}


.containerCard {
  flex-grow: 1;
  flex-shrink: 1;
  height: 100%;
  padding: 10px;
  overflow: hidden;
}

.aMap {
  width: 100%;
  height: 80vh;
  font-family: "微软雅黑";
  overflow: hidden;
  border-radius: 10px;
  transition: 0.3s;
}

.chart {
  width: 100%;
  height: 80vh;
  overflow: hidden;
  border-radius: 10px;
  transition: 0.3s;
}

.controlButton {
  outline: none;
  border: none;
  background-color: #fff;
  background: none;
  width: 30px;
  height: 30px;
  font-size: 16px;
  font-weight: bold;
  text-align: center;
  border: 1px solid #5177d2;
  border-radius: 100%;

  .controlIcon {
    color: #5177d2;
  }
}

.controlButton:hover {
  cursor: pointer;

  .controlIcon {
    color: #9097a5;
  }
}

.controlButton:active .controlIcon {
  color: #000;
}

.controlButtonDis:hover {
  cursor: default;

}
</style>
